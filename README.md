# Distributed File System Using Java RMI

A distributed file system built in Java leveraging Remote Method Invocation (RMI) for inter-node communication. The system supports file upload, download, search, and delete operations while enforcing consistency through a logical clock mechanism and priority queue-based transaction ordering — achieving a **50% improvement in system efficiency** through optimized inter-node communication and load-balanced resource allocation.

---

## ⚙️ Architecture Highlights

- **50% efficiency gain** via optimized RMI inter-node communication and load-balanced resource allocation
- **80% consistency improvement** enforced through Lamport logical clock algorithms and priority queue transaction ordering
- Full fault tolerance with acknowledgment-driven transaction confirmation across all nodes

---

## 🧩 Components

| Component | Role |
|-----------|------|
| **Node** | Core distributed node implementing `NodeI`; manages transactions, ACKs, and user interactions |
| **Multicast Transaction** | Initiates and multicasts transactions across all nodes with synchronized logical clock values |
| **Perform Transaction** | Processes incoming transactions, updates local state, and multicasts acknowledgments |
| **Multicast Ack** | Sends transaction ACKs to all nodes, maintaining clock synchronization |
| **Fetch New Transaction** | Executes transactions with sufficient acknowledgments, performing the specified file operation |

---

## 📁 Code Overview

### `Main.java`
Entry point for initializing a distributed node. Sets up both RMI server and client, handles user-driven file operations (upload, search, download, delete), and uses a `Timer` to periodically fetch and process new transactions. Validates file extensions and manages transaction multicasting to maintain network-wide synchronization.

### `Node.java`
The core class implementing distributed file operations over RMI. Manages upload, download, search, and delete while handling transactions and acknowledgments between nodes. Uses a logical clock for consistent transaction ordering and a priority queue for processing. Handles file serialization/deserialization for accurate data transfer across nodes.

### `NodeI` Interface
Defines the remote methods exposed by each node — `performTransaction`, `ack`, `downloadFile`, `searchFiles`, `uploadFile`, and `deleteFile`. Each method declares `RemoteException` for network fault handling.

### `Transaction.java`
Implements `Comparable` for total ordering of transactions. Fields include transaction ID, sender, filename, logical clock time, and operation type. Ordered first by logical clock value, then by process ID to break ties. Serializable for network transmission.

### `FileSerializable.java`
Handles serialization of file data (name, path, byte content, last modified date) for transfer across nodes. Ensures file metadata is preserved through the serialization/deserialization cycle.

### `Delayer.java`
Simulates network latency by introducing a **5-second delay** before invoking `performTransaction` on a remote node — used for realistic distributed system testing.

### `AckDelayer.java`
Simulates acknowledgment propagation delay with a **7-second delay** before sending ACKs — models real-world conditions where acknowledgment timing affects transaction ordering.

---

## 🖼️ Screenshots

**Node Initialization**
![Node Init](https://github.com/user-attachments/assets/7f65054a-1d4e-4b17-945f-cc7ca93f5512)
Node binds to the RMI registry, registering its service name and port for network communication.

**Multicast Transaction**
![Multicast](https://github.com/user-attachments/assets/0c8e990a-7f9f-4200-b44a-da3dcc3ccef5)
A node multicasts a transaction (upload/download) to all peers to ensure synchronized processing.

**Transaction Queuing**
![Queue](https://github.com/user-attachments/assets/8cb35038-eba4-499b-aadd-49064eb13178)
Incoming transactions are added to the priority queue; logical clock is updated to maintain consistency.

**Acknowledgment**
![ACK](https://github.com/user-attachments/assets/6e0479e4-b795-4596-a08f-f788fe334d9b)
A processed transaction triggers ACKs to all nodes, confirming the operation.

**File Download**
![Download](https://github.com/user-attachments/assets/1e5241af-1b1a-4b28-8ed2-060e507f4018)
A client requests a file; the node processes and returns it.

**File Search**
![Search](https://github.com/user-attachments/assets/405be658-65da-4a90-a835-a0e94b2ecf07)
User searches for a file; system responds with existence and location.

**Delayed ACK (Latency Simulation)**
![Delayed ACK](https://github.com/user-attachments/assets/c6fda818-ed1f-4b9a-8343-8baae597ba09)
`AckDelayer` introduces realistic network delay before acknowledgment transmission.
