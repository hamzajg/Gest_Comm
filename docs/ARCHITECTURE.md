# Architecture — Gest_Comm

Current-state map. Descriptive, not aspirational; see `docs/adr/` for why.

## System shape

```
Gest_Comm_ClientFX (JavaFX desktop client, sibling repo)
        │  HTTP/REST JSON  (/rest/*)
        ▼
RESTWebService/  ADF web application (WebLogic)
   └─ web.xml: /rest/* → oracle.adf.model.servlet.rest.ResourceServlet
        │  ADF runtime generates REST resources from the model
        ▼
RESTModel/  ADF BC4J model
   ├─ entities/Article.xml          entity over the `article` table
   ├─ entities/views/ArticleView.xml  view object (the REST-exposed shape)
   └─ services/AppModule.xml         application module (wiring)
        │
        ▼
MySQL gest_comm_db (com.mysql.jdbc.Driver)
```

## Design

- **Metadata-driven, not code-driven.** The "API" is whatever ADF's
  `ResourceServlet` generates from `AppModule` + view objects. There are
  no controllers, no serializers, no routes to hand-write.
- **Entity → ViewObject → AppModule** is the BC4J layering: entity maps
  the table, view object shapes the exposed attributes, the app module
  bundles them for the service.
- **Skeleton scope.** One entity (`Article`) and one view object
  (`ArticleView`) exist; the domain's other nouns (customers, delivery
  notes, invoices) are unmodelled.

## State

MySQL `gest_comm_db`. DB connection defined in
`.adf/META-INF/connections.xml`. No migrations in the Maven sense —
schema changes are JDeveloper entity-regeneration + manual DDL.

## Contract with the client

The contract is implicit: whatever JSON ADF's ResourceServlet emits for
`ArticleView`. Since `Gest_Comm_ClientFX` doesn't call the API yet, there
is **no stable contract to preserve** — but once the client implements
calls, ADF's generated shapes become the de-facto schema and changes need
cross-repo coordination.
