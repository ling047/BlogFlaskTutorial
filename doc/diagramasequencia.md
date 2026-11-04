
# flask --app flaskr init-db

```Mermaid
sequenceDiagram
    autonumber
    actor User as Usuario / Terminal
    participant Flask as Flask CLI
    participant App as flaskr/__init__.py
    participant DB as flaskr/db.py
    participant SQLite as SQLite
    participant Schema as schema.sql

    User->>Flask: flask --app flaskr init-db
    Flask->>App: Cargar aplicación
    App->>App: create_app()
    App->>DB: db.init_app(app)
    DB->>Flask: Registrar init_db_command
    DB->>App: Registrar close_db
    App-->>Flask: Devolver aplicación

    Flask->>Flask: Crear Application Context
    Flask->>DB: Ejecutar init_db_command()
    DB->>DB: init_db()
    DB->>DB: get_db()
    DB->>SQLite: sqlite3.connect(DATABASE)
    SQLite-->>DB: Devolver conexión
    DB->>App: open_resource("schema.sql")
    App-->>DB: Devolver archivo SQL
    DB->>Schema: Leer instrucciones SQL
    Schema-->>DB: DROP TABLE / CREATE TABLE
    DB->>SQLite: executescript(SQL)
    SQLite-->>DB: Ejecutar instrucciones
    DB-->>DB: init_db() finaliza
    DB->>Flask: click.echo("Initialized the database.")
    Flask-->>User: Initialized the database.

    Flask->>DB: teardown_appcontext(close_db)
    DB->>SQLite: db.close()
    SQLite-->>DB: Conexión cerrada
```
# get
```Mermaid
sequenceDiagram
    autonumber
    actor User as Usuario
    participant Browser as Navegador
    participant Flask as Flask
    participant Auth as auth.py
    participant Blog as blog.py
    participant DB as db.py
    participant SQLite as SQLite
    participant Jinja as Jinja2

    User->>Browser: Accede a /
    Browser->>Flask: GET / HTTP Request
    Flask->>Flask: Buscar ruta correspondiente
    Note over Flask,Blog: Endpoint: blog.index

    Flask->>Auth: load_logged_in_user()
    Auth->>Auth: Obtener user_id de session

    alt Usuario autenticado
        Auth->>DB: get_db()
        DB->>SQLite: SELECT * FROM user WHERE id = ?
        SQLite-->>DB: Datos del usuario
        DB-->>Auth: User
        Auth->>Auth: Guardar usuario en g.user
    else Usuario no autenticado
        Auth->>Auth: g.user = None
    end

    Flask->>Blog: index()
    Blog->>DB: get_db()
    DB-->>Blog: Conexión SQLite

    Blog->>SQLite: SELECT posts + users<br/>JOIN user ORDER BY created DESC
    SQLite-->>Blog: Lista de publicaciones
    Blog->>Blog: fetchall()

    Blog->>Jinja: render_template(index.html, posts)
    Jinja->>Jinja: Cargar base.html
    Jinja->>Jinja: Renderizar publicaciones
    Jinja->>Jinja: Comprobar g.user y autoría
    Jinja-->>Blog: HTML generado

    Blog-->>Flask: Devolver HTML
    Flask->>Flask: Crear HTTP Response (200 OK)
    Flask-->>Browser: HTTP 200 + HTML
    Browser->>Browser: Mostrar página principal

    Flask->>DB: close_db() al finalizar contexto
    DB->>SQLite: Cerrar conexión
```