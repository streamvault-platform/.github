# StreamVault
_under active development - not production ready yet_


Self-hostable music streaming platform with native smartwatch offline sync.

Deploy in one command via Docker Compose. Production-ready on Kubernetes via Helm.
Open source, Apache 2.0.

---

## Repositories

| Repo | Stack | What it does |
|------|-------|--------------|
| [streamvault-infra](https://github.com/streamvault-platform/infra) | Docker Compose · Helm · Envoy | All deployment artifacts. Single source of truth for running the platform. Start here. |
| [streamvault-core](https://github.com/streamvault-platform/core) | Java · Quarkus · PostgreSQL · Redis | Core API: auth, media library, file upload, audio streaming, WebSocket playback state |
| [streamvault-pipeline](https://github.com/streamvault-platform/pipeline) | Scala 3 · ZIO 2 · fs2-kafka · FFmpeg | Async media processing: metadata extraction, transcoding, watch sync queue |
| [streamvault-web](https://github.com/streamvault-platform/web) | Expo · React Native · TypeScript | Cross-platform client for web, iOS, and Android |
| [streamvault-wearos](https://github.com/streamvault-platform/wearos) | Kotlin · Jetpack Compose for Wear | Native Wear OS offline player with smartwatch sync *(post-MVP)* |
| [streamvault-analytics](https://github.com/streamvault-platform/analytics) | Scala 3 · Spark 3 · Delta Lake | Play event ingestion and ML-based recommendations *(post-MVP)* |

---

## How the pieces fit together

```
Web / Mobile / Wear OS clients
        │
      [Envoy]          ← single entry point, TLS, routing, rate limiting
        │
  [streamvault-core]   ← REST + WebSocket + JWT auth (Java · Quarkus)
        │
      [Kafka]          ← all async events (KRaft mode, no Zookeeper)
        │
[streamvault-pipeline] ← media processing: transcode, metadata, watch sync (Scala · ZIO 2)
```

**Infrastructure always required:** PostgreSQL · Kafka · RustFS (S3-compatible object storage) · Redis · Envoy

**Optional (production):** Prometheus · Grafana · Kafka UI

---

## Quick start

```sh
git clone https://github.com/streamvault-platform/infra
cd infra
cp .env.example .env   # fill in passwords
docker compose --profile standard up -d
```

The full stack (Postgres, Kafka, Redis, RustFS, Envoy, core, pipeline) starts in one command.
Point the web client at `http://localhost:8080`.

For Kubernetes:
```sh
helm install streamvault ./helm --namespace streamvault --create-namespace 
```

---

## License

Apache 2.0 — see [LICENSE](LICENSE)
