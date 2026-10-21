# Model de Dades

## Model Entitat Relació E/R

```mermaid

erDiagram
    User || --o{ Post :id
    User{
        
        string username
        string password
    }
    Post{
        
        date created
        string title
        string body
    }
```
    Quin tipus de Base de dades utilizan l'aplicació