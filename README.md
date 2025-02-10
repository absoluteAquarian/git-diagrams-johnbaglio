# Pokémon Card Storage Web App
### Entity-Relationship Diagram
```mermaid
erDiagram
    USER {
        string username
        string password
    }
    CARD {
        int id PK
        string pokemon_name
        int[] pokemon_types
        int rarity
        int pokemon_health
        int[] pokemon_moves
    }
    DECK {
        string name
        int[] cards
    }
    ADMIN {
        string username
        string password
    }
    USER ||--o{ CARD : submits
    USER }|--|{ CARD : views
    USER ||--o{ DECK : assembles
    USER ||--o{ DECK : manages
    DECK ||--|{ CARD : contains
    ADMIN ||--o{ USER : manages
    ADMIN ||--o{ CARD : creates
    ADMIN ||--o{ CARD : modifies
    ADMIN ||--o{ CARD : deletes
```
### User Flow Diagram
```mermaid
flowchart TD
    subgraph card-view ["Viewing Cards"]
        direction LR
        cv1["User selects card"]
        cv2["Card gets displayed to the user"]
        cv1-->cv2
        cv3{"Is the user an admin?"}
        cv2-->cv3
        cv3-->|No|cv2
        cva1["Display delete button"]
        cv3-->|Yes|cva1
        cva2["Card gets deleted from the database"]
        cva1-->|click button|cva2
    end
    subgraph creating-decks ["Creating Card Decks"]
        direction LR
        cd1["User creates deck"]
        cd2["Deck modification"]
        cd1-->cd2
        cdr1["Card gets removed from the deck"]
        cd2-->|remove a card|cdr1
        cdr1-->cd2
        cd3["Deck gets saved to the user's account"]
        cd2-->|save deck|cd3
        cd4["Return to main menu"]
        cd3-->cd4
        cda1{"Does the user own the card?"}
        cd2-->|add a card|cda1
        cda2["Card gets added to the deck"]
        cda1-->|Yes|cda2
        cda2-->cd2
        cda3["Card addition gets rejected"]
        cda1-->|No|cda3
        cda3-->cd2
    end
    subgraph creating-cards ["Creating New Cards"]
        cc1["User wants to create a new card"]
        cc2{"Does the card already exist?"}
        cc1-->cc2
        cc3["Card submission request gets sent to the database"]
        cc2-->|No|cc3
        ccr1["Reject card creation"]
        cc2-->|Yes|ccr1
        cc4["Admin views card submission"]
        cc5{"Is the card for an existing Pokémon, NPC, item or location in the Pokémon games?"}
        cc4-->cc5
        cc5-->|No|ccr1
        cca1["Add new card to database"]
        cc5-->|Yes|cca1
    end
```
### System Architecture Diagram
```mermaid
architecture-beta
    group frontend(internet)[Userspace]
    group backend(internet)[Backend]
    group admin(server)[Admin Dashboard]

    service api_endpoints_user(internet)[API Endpoints] in frontend
    service user_interface_user(server)[User Interface] in frontend
    service api_endpoints_backend(internet)[API Endpoints] in backend
    service database(database)[Database] in backend
    service db_storage(disk)[Data Storage] in backend
    service api_endpoints_admin(internet)[API Endpoints] in admin
    service user_interface_admin(server)[User Interface] in admin

    junction junction_api

    junction_api:L -- R:api_endpoints_user
    api_endpoints_user:L -- R:user_interface_user
    junction_api:B -- T:api_endpoints_admin
    api_endpoints_admin:L -- R:user_interface_admin
    junction_api:R -- L:api_endpoints_backend
    api_endpoints_backend:T -- B:database
    database:R -- L:db_storage
```
### API Endpoints Table
| Endpoint | HTTP Method | Requirements
|----------|-------------|-------------
| Fetch Pokémon cards | `GET` | None
| Create card deck | `POST` | User
| Update card deck | `POST` | User
| Save card deck | `POST` | User
| Submit card request | `POST` | User
| Add new card | `POST` | Administrator
| Delete card | `POST` | Administrator
| Fetch card submissions | `GET` | Administrator
| Accept/reject card submission | `POST` | Administrator
