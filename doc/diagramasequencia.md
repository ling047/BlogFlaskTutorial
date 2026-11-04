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