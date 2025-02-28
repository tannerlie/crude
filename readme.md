# crude

**crude** is a blockchain built using Cosmos SDK and Tendermint and created with [Ignite CLI](https://ignite.com/cli).

## Installation

### Prerequisites
To set up and run the `crude` blockchain, ensure you have the following installed:

- [Go](https://go.dev/doc/install) (latest version recommended)
- [Ignite CLI](https://ignite.com/cli)
- [Git](https://git-scm.com/downloads)
- [Docker](https://www.docker.com/get-started) (optional but recommended for testing)

### Install Ignite CLI
If you haven’t installed Ignite CLI, run:
```
curl https://get.ignite.com/cli! | bash
```

### Clone the Repository
```
git clone https://github.com/tannerlie/crude.git
cd crude
```

## Running the Blockchain

### Start the Blockchain in Development Mode
```
ignite chain serve
```

The `serve` command installs dependencies, builds, initializes, and starts your blockchain in development mode.

### Reset the Blockchain
To reset the blockchain state, run:
```
ignite chain serve --reset-once -v
```

## CRUD Operations
The `crude` blockchain implements CRUD operations for managing posts. The following messages are available:

- **CreatePost**: Adds a new post with a title, content, and creator.
- **UpdatePost**: Modifies an existing post by its ID.
- **DeletePost**: Removes a post by its ID.
- **ShowPost**: Retrieves a specific post by its ID.
- **ListPost**: Displays all posts.

### Example Commands

Before running any commands, make sure you have identified the directory where the `cruded` binary is located. Typically, it can be found at:
```
$HOME/go/bin/cruded
```

**Create a Post:**
```
$HOME/go/bin/cruded tx crude create-post "My Title" "This is the content" --from alice --chain-id crude
```

**Update a Post:**
```
$HOME/go/bin/cruded tx crude update-post 0 "Updated Title" "Updated Content" --from alice --chain-id crude
```

**Delete a Post:**
```
$HOME/go/bin/cruded tx crude delete-post 0 --from alice --chain-id crude
```

**Show a Post:**
```
$HOME/go/bin/cruded q crude show-post 0
```

**Show the List of Posts:**
```
$HOME/go/bin/cruded q crude list-post
```

## Consensus-Breaking Changes
### What is a Consensus-Breaking Change?
A consensus-breaking change occurs when different nodes running different versions of the blockchain software disagree on the blockchain state. This typically happens due to changes in transaction processing, state storage, or cryptographic validation.

### Example: Changing Post ID Type
One example of a consensus-breaking change in `crude` is modifying the `id` field in the `Post` struct from `string` to `uint64`.

#### Before:
```go
type Post struct {
    Id      string `json:"id"`
    Title   string `json:"title"`
    Content string `json:"content"`
    Creator string `json:"creator"`
}
```

#### After:
```go
type Post struct {
    Id      uint64 `json:"id"`
    Title   string `json:"title"`
    Content string `json:"content"`
    Creator string `json:"creator"`
}
```

### Why Does This Break Consensus?
- **State Storage Incompatibility**: Old nodes store `Id` as a string, but new nodes expect an integer (`uint64`).
- **Transaction Processing Mismatch**: Old transactions use `string` IDs, but new nodes expect `uint64`, causing failed verifications.
- **Blockchain Fork Risk**: If not all nodes upgrade simultaneously, the network may split into incompatible chains.

To prevent issues, consensus-breaking changes require a **hard fork** or state migration.

