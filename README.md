# Gest_Comm

The back end of a *gestion commerciale* (small-business sales management)
application: an Oracle ADF/BC4J model exposed over REST by an ADF web
application. The desktop client lives in
[Gest_Comm_ClientFX](../Gest_Comm_ClientFX).

This is a JDeveloper workspace, not a Maven or Gradle project. It is a
skeleton: one entity (`Article`), one view object over it, and one application
module. Nothing else in the domain (customers, delivery notes, invoices) has
been modelled yet.

## Requirements

- Oracle JDeveloper 12.2.1.x with the ADF and BC4J extensions
- a MySQL database named `gest_comm_db` (the connection uses
  `com.mysql.jdbc.Driver`)
- an integrated or standalone WebLogic Server with the `adf.oracle.domain`
  shared library

There is no CI workflow: compiling this project needs the proprietary ADF
libraries that ship with JDeveloper, so it cannot be built on a plain JDK.

## Open and run it

1. Open `Gest_Comm.jws` in JDeveloper.
2. Check the `GestCommDB` connection in the Resource Palette: host `localhost`,
   schema `gest_comm_db`, user `root`. JDeveloper stores its password in an
   Oracle credential wallet (`src/META-INF/cwallet.sso`), which is **not**
   tracked by git — it is recreated on the first successful connection.
3. The database needs an `article` table with at least
   `id_article BIGINT NOT NULL PRIMARY KEY`.
4. Run `RESTWebService/RESTWebService.jpr` on the integrated WebLogic Server.
   The ADF REST servlet is mapped at `/rest/*`.

## Layout

```
Gest_Comm.jws                       JDeveloper workspace
RESTModel/RESTModel.jpr             BC4J model project
  src/.../entities/Article.xml      entity over the `article` table
  src/.../entities/views/ArticleView.xml
  src/.../services/AppModule.xml    application module
  src/.../services/common/bc4j.xcfg JDBC data source + pooling config
RESTWebService/RESTWebService.jpr   web application project
  public_html/WEB-INF/web.xml       ADF REST servlet at /rest/*
.adf/META-INF/connections.xml       the GestCommDB IDE connection
src/META-INF/jps-config.xml         credential store configuration
src/META-INF/weblogic-application.xml  `adf.oracle.domain` library reference
```

## Status

| Piece | State |
|---|---|
| `Article` entity | only `id_article` is mapped; the business columns are not |
| `ArticleView` | exposes `id_article` only |
| `AppModule` | exists, no custom methods |
| REST surface | servlet wired; no resource classes declared yet |
| Client, delivery notes, invoices | not modelled |

## Credentials

`connections.xml` contains no password — only the credential-store key
`GestCommDB` and the user name. The wallet that holds the password was removed
from the repository; see the security PR for the rotation note.

## License

MIT — see [LICENSE](LICENSE).
