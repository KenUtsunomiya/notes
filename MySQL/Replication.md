# MySQL Replication: Step-by-Step

MySQL’s single‑source replication involves dedicated threads on the source and on each replica, working together to ship and apply changes.

## 1. Write to the Binary Log (Source)
- **What happens**  
  When a transaction commits or a DDL statement runs, MySQL records the change in its binary log (`binlog`) according to `binlog_format` (ROW, STATEMENT, or MIXED).
- **Why it matters**  
  The binlog is the _source of truth_ for replication—replicas read from it.

## 2. Binlog Dump Thread (Source)
- **Thread name**: `Binlog Dump` (visible in `SHOW PROCESSLIST`)
- **Role**  
  Streams newly written binlog events over the network to each replica that has issued `START REPLICA`.

## 3. I/O Thread → Relay Log (Replica)
- **Thread name**: I/O (receiver) thread  
- **What happens**  
  Connects to the source’s Binlog Dump thread, fetches binlog events, and writes them into the replica’s local **relay log** files.
- **Why it matters**  
  Decouples fetching from applying—so if applying is slow, the I/O thread can continue to pull new events.
- **Status check**: `Replica_IO_Running: Yes` in `SHOW REPLICA STATUS`

## 4. SQL Thread → Apply Events (Replica)
- **Thread name**: SQL (applier) thread  
- **What happens**  
  Reads events from the relay log and executes them against the replica’s data files.
- **Parallelism**  
  If `replica_parallel_workers > 0`, a coordinator plus worker threads apply transactions in parallel; otherwise there’s a single SQL thread.
- **Status check**: `Replica_SQL_Running: Yes` in `SHOW REPLICA STATUS`

---

## Additional Concepts

### GTID Auto‑Positioning
- With GTIDs enabled (`gtid_mode=ON`), replicas send their executed GTID set; the source then streams only the _missing_ transactions—eliminating manual file/position setup.

### Semi‑Synchronous Acknowledgment
- In semi‑sync mode, after writing to the binlog the source blocks the commit until at least one replica’s I/O thread has received and flushed the event to its relay log (not yet applied).

### Monitoring & Lag
- **Replication lag**: `Seconds_Behind_Master` in `SHOW REPLICA STATUS`  
- **Semi‑sync metrics**: `SHOW STATUS LIKE 'Rpl_semi_sync_%'`  
