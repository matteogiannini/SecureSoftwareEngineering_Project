# Lady Gatcha — Secure Software Engineering Project

Security-focused engineering of the **Lady Gatcha** gacha web game: a microservices application developed for the *Secure Software Engineering* course, Master's Degree in Cybersecurity, University of Pisa.

The project takes the gacha collection-and-trading game (players open packs to collect Lady Gaga vinyl records of varying rarity and trade them on an in-game market) and engineers it with security as a first-class concern, applying secure design principles, an authentication/authorisation layer, and an automated test and CI pipeline.

## Security focus

- **Authentication & authorisation** — centralised JWT-based auth; every service validates tokens through a shared `auth_utils` module (CI enforces that all copies of `auth_utils.py` are byte-identical).
- **Transport security** — services exposed over TLS/HTTPS (self-signed certs for local development).
- **Gateway isolation** — separate public (`gateway`) and administrative (`admin-gateway`) entry points to segregate user and admin attack surfaces.
- **Service isolation** — each microservice is independently containerised and individually testable, limiting blast radius.
- **Automated testing in CI** — integration and per-service isolation test suites run on every push via GitHub Actions, plus Locust load testing.

## Architecture

| Service          | Responsibility                                         |
| ---------------- | ------------------------------------------------------ |
| `auth`           | Authentication and JWT issuance                        |
| `user`           | User accounts and profiles                             |
| `gatcha`         | Pack opening, collection, rarity logic                 |
| `market`         | In-game marketplace and trading                        |
| `gateway`        | Public API gateway / reverse proxy                     |
| `admin-gateway`  | Administrative API gateway                             |
| `client`         | Browser client                                         |
| `shared`         | Shared utilities (`auth_utils`, sync logic)            |

## Tech stack

- **Backend:** Python (Flask), JWT authentication, TLS/HTTPS
- **Containerisation:** Docker & Docker Compose
- **Testing:** Postman/Newman collections (integration + isolation), Locust load testing
- **CI:** GitHub Actions

## Repository structure

```
.
├── src/                 # Microservices (auth, user, gatcha, market, gateway, admin-gateway, client, shared)
│   ├── docker-compose.yml   # Orchestrates the full system
│   └── <service>/           # Each service: app.py, Dockerfile, docker-compose.yml, requirements.txt
├── docs/                # Report, OpenAPI spec, architecture, Postman collections, Locust script
├── .github/workflows/   # CI pipeline (integration & unit tests)
└── README.md
```

## Get Started

1. Build and start the full system:

   ```shell
   cd SecureSoftwareEngineering_Project/src
   docker compose up --build
   ```

2. From the repository **root**, run the integration tests:

   ```shell
   newman run docs/integration-tests.postman_collection.json -e docs/localhost-ema-with-https.postman_environment.json --insecure
   ```

3. Access the browser client on `localhost:8080`.

## Isolation Testing

Each microservice can be built and tested in isolation:

```shell
cd src/<service>            # auth / gatcha / market / user
docker compose up --build
# then, from the repo root:
newman run docs/isolation-<service>-service.postman_collection.json -e docs/localhost-ema-with-https.postman_environment.json --insecure
```

## The `/docs` folder

- `architecture.yml` — architecture model (importable in MicroFreshner).
- `Gaga OpenAPI.yaml` — OpenAPI specification of the REST API.
- `Lady Gatcha Report.pdf` — detailed project report.
- `locustfile.py` — Locust performance / rolling-probability test script.
- Postman collections and environment for integration and isolation testing.
- `test.jpg` — image used by the Postman tests.

## Authors

Developed by the project team. Repository maintained on GitHub.
