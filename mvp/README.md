# MVP orkiestracji self-hosted infra DAO (Fala 5)

Minimalny, **dzialajacy** rdzen infrastruktury HeadQuoter SpaceCraft. Zamiast pelnej
wizji z [glownego README](../README.md) (NextCloud, Mattermost, WireGuard,
LibreTranslate, LDAP, ...) uruchamiasz trzy filary, ktore wystarczaja, by DAO
zaczela realnie pracowac:

| Serwis  | Rola w DAO                                        | Port (domyslny)     |
|---------|---------------------------------------------------|---------------------|
| Gitea   | SVN/git self-host — zrodla projektow, kod, wiki   | 3000 (HTTP), 2222 (SSH) |
| n8n     | automatyzacja — workflow, integracje, orkiestracja| 5678                |
| Ollama  | LLM self-host — lokalna inteligencja dla org      | 11434               |

Dodatkowo w tle: PostgreSQL jako baza dla Gitea (`gitea-db`).

## Wymagania

- Docker + wtyczka Docker Compose v2 (`docker compose`, nie `docker-compose`)
- ~4 GB RAM wolne (wiecej jesli bedziesz uruchamiac wieksze modele w Ollama)

## Start

```bash
cd mvp
cp .env.example .env
# edytuj .env — zmien wszystkie changeme_* na wlasne sekrety
docker compose up -d
```

Sprawdz stan (kolumna STATUS powinna pokazywac `healthy`):

```bash
docker compose ps
```

Dostep:

- Gitea:  http://localhost:3000  (pierwszy zarejestrowany uzytkownik = admin)
- n8n:    http://127.0.0.1:5678  (patrz nizej — port zbindowany tylko do localhost)
- Ollama: http://localhost:11434 (API; `curl http://localhost:11434/api/tags`)

### Dostep do n8n i zakladanie konta ownera

n8n w wersji >=1.0 **nie ma juz basic-auth** (stare zmienne `N8N_BASIC_AUTH_*`
zostaly usuniete w v1.0 i sa cicho ignorowane). Uwierzytelnianie realizuje wbudowany
system User Management: **pierwsza osoba, ktora otworzy panel, zaklada konto ownera**.

Dlatego port n8n jest w `docker-compose.yml` zbindowany **tylko do `127.0.0.1`**
(`127.0.0.1:5678:5678`) — panel nie jest wystawiony na zadnym innym interfejsie hosta.
Konto ownera zaloz przy pierwszym wejsciu jednym z bezpiecznych sposobow:

- lokalnie na hoscie: otworz `http://127.0.0.1:5678` i wypelnij formularz setupu ownera;
- zdalnie: przez **tunel SSH**, np. `ssh -L 5678:127.0.0.1:5678 user@host`, a potem
  `http://127.0.0.1:5678` w lokalnej przegladarce.

**Produkcja:** nie zmieniaj bindu na `0.0.0.0` — wystaw n8n za **reverse-proxy z
wlasnym uwierzytelnianiem** (np. Traefik/nginx + auth) albo za VPN. Odslonecie panelu
bez auth pozwoliloby przypadkowemu odwiedzajacemu zalozyc konto ownera i przejac
instancje.

Pobierz pierwszy model do Ollama:

```bash
docker exec -it hq-ollama ollama pull llama3.2
docker exec -it hq-ollama ollama run llama3.2 "Czesc"
```

Z poziomu n8n Ollama jest dostepne wewnatrz sieci pod adresem `http://ollama:11434`
(zmienna `OLLAMA_HOST` jest juz ustawiona w kontenerze n8n).

## Stop / czyszczenie

```bash
docker compose down            # zatrzymaj (dane w wolumenach zostaja)
docker compose down -v         # zatrzymaj i USUN wolumeny (kasuje dane!)
```

## Co jest w MVP, a co „later"

**W MVP (rdzen Fala 5):**
- Gitea + PostgreSQL — self-hosted SVN/git
- n8n — automatyzacja
- Ollama — lokalny LLM
- Jedna sic bridge (`hq-mvp`), nazwane wolumeny, healthchecki, konfiguracja przez `.env`

**Later (pelna wizja — patrz glowny README):**
- NextCloud — cloud storage
- Mattermost — czat wewnetrzny
- OpenWebUI — interfejs do LLM / vector DB
- WireGuard — VPN dla federacji branchy
- Traefik — reverse proxy + TLS
- LibreTranslate — tlumaczenia
- SearXNG, Vaultwarden, Wiki.js, Sentry, LDAP, ... (`resources/setups/`, `src/services/`)

## Jak rozszerzac do pelnej wizji

Ten MVP jest celowo samodzielny (osobny `name:` i sic), zeby nie kolidowac z
korzeniowym `compose.yaml`, ktory sklada pelny stos przez `include:`
(`resources/drivesets`, `resources/setups`, `src/services`, `src/networking`).

Sciezki rozwoju:

1. **Krok po kroku w MVP** — dodawaj kolejne serwisy do `mvp/docker-compose.yml`
   (np. `openwebui` wskazujacy na `ollama:11434`, potem `traefik` jako proxy).
2. **Migracja do pelnego stosu** — gdy rdzen dziala, przenies definicje do
   modularnych plikow w `src/services/*/compose.yaml` i wpinaj je przez `include:`
   w korzeniowym `compose.yaml` (tak jak zaprojektowano infraforest).
3. **Reverse proxy** — dodaj Traefik (`src/networking/traefik/`) i wystaw serwisy
   pod domenami zamiast portow, dopnij TLS.
4. **Sekrety produkcyjne** — zastap `.env` menedzerem sekretow; wlacz
   `GITEA__server__ROOT_URL` / `N8N_WEBHOOK_URL` z realnymi domenami.

## Bezpieczenstwo

- Wszystkie `changeme_*` w `.env.example` to placeholdery. Zmien je przed
  jakimkolwiek wystawieniem poza `localhost`.
- `.env` nie jest commitowany.
- **n8n nie ma basic-auth** (usuniete w n8n v1.0). Dlatego jego port jest zbindowany
  tylko do `127.0.0.1` — konto ownera zakladasz lokalnie / przez tunel SSH, a na
  produkcji wystawiasz go za reverse-proxy z auth lub VPN (patrz sekcja „Dostep do n8n").
- Ollama i Gitea sluchaja na wszystkich interfejsach hosta przez mapowanie portow —
  na produkcji schowaj je za proxy/VPN.
- Obrazy sa przypiete do konkretnych wersji (Gitea, PostgreSQL, n8n `1.70.0`,
  Ollama `0.5.7`) — bez `latest`, dla powtarzalnosci i kontroli aktualizacji.
