<h1>Sinkronisasi pada Sistem terdistribusi</h1>

<h3>3.1 Sinkronisasi Waktu</h3>

<img width="499" height="356" alt="sinkronisasi waktu" src="https://github.com/user-attachments/assets/64414fa6-06ce-4a1c-9aa0-57888409d256" />

pembahasan :

Proses sinkronisasi waktu pada sistem operasi Windows menggunakan aplikasi NetTime berjalan dengan sukses. Berdasarkan hasil pengujian pada aplikasi, komputer berhasil terhubung ke server acuan 0.nettime.pool.ntp.org dengan nilai lag jaringan sebesar 52 ms. Terdeteksi adanya offset sebesar -1033 ms yang kemudian dikoreksi secara otomatis oleh protokol NTP, sehingga jam lokal komputer kembali akurat dan tersinkronisasi penuh (Time is synchronized).

<br></br>

<h3>3.2 vector Clock</h3>

<i>vclocks.py</i>
```bash
class VectorClock:
    def __init__(self, num_processes, process_id):
        self.clocks = [0] * num_processes  # Initialize all clocks to zero
        self.process_id = process_id      # The ID of the current process

    def increment(self):
        """Increments the local clock for the current process."""
        self.clocks[self.process_id] += 1

    def send_message(self):
        """Prepares the vector clock for sending with a message."""
        self.increment()  # Increment local clock before sending
        return list(self.clocks) # Return a copy of the current vector clock

    def receive_message(self, received_clocks):
        """Updates the vector clock upon receiving a message."""
        for i in range(len(self.clocks)):
            self.clocks[i] = max(self.clocks[i], received_clocks[i])
        self.increment()  # Increment local clock after receiving and merging

    def __str__(self):
        return f"P{self.process_id}: {self.clocks}"

    def happens_before(self, other_vector_clock):
        """
        Determines if this vector clock happens before another.
        A happens before B if all elements in A are less than or equal to
        the corresponding elements in B, and at least one element in A is
        strictly less than the corresponding element in B.
        """
        if self.clocks == other_vector_clock.clocks:
            return False  # Same clocks, not "happens before"

        all_le = True  # All elements are less than or equal
        any_lt = False # At least one element is strictly less than

        for i in range(len(self.clocks)):
            if self.clocks[i] > other_vector_clock.clocks[i]:
                return False # Not "happens before" if any element is greater

            if self.clocks[i] < other_vector_clock.clocks[i]:
                any_lt = True

        return all_le and any_lt

    def is_concurrent(self, other_vector_clock):
        """
        Determines if two vector clocks are concurrent.
        Two events are concurrent if neither happens before the other.
        """
        return not (self.happens_before(other_vector_clock) or
                    other_vector_clock.happens_before(self))

# Simulate a system with 3 processes
vc0 = VectorClock(num_processes=3, process_id=0)
vc1 = VectorClock(num_processes=3, process_id=1)
vc2 = VectorClock(num_processes=3, process_id=2)

print(vc0)
print(vc1)
print(vc2)

# Process 0 performs a local event
vc0.increment()
print(vc0)

# Process 0 sends a message to Process 1
message_from_p0 = vc0.send_message()
vc1.receive_message(message_from_p0)
print(vc0)
print(vc1)

# Process 2 performs a local event
vc2.increment()
print(vc2)

# Process 1 sends a message to Process 2
message_from_p1 = vc1.send_message()
vc2.receive_message(message_from_p1)
print(vc1)
print(vc2)

# Check causality
print(f"VC0 happens before VC1: {vc0.happens_before(vc1)}")
print(f"VC1 happens before VC0: {vc1.happens_before(vc0)}")
print(f"VC0 is concurrent with VC2: {vc0.is_concurrent(vc2)}")
```
**Output :**
```bash
P0: [0, 0, 0]
P1: [0, 0, 0]
P2: [0, 0, 0]
P0: [1, 0, 0]
P0: [2, 0, 0]
P1: [2, 1, 0]
P2: [0, 0, 1]
P1: [2, 2, 0]
P2: [2, 2, 2]
VC0 happens before VC1: True
VC1 happens before VC0: False
VC0 is concurrent with VC2: False
```
pembahasan :

Pada eksekusi program Vector Clock, kondisi awal proses P0, P1, dan P2 memiliki nilai vektor [0, 0, 0]. Ketika P0 melakukan event, nilai vektornya berubah menjadi [1, 0, 0], kemudian menjadi [2, 0, 0]. Setelah P1 menerima informasi dari P0, vektornya menjadi [2, 1, 0]. Sementara itu, P2 melakukan event lokal sehingga nilainya menjadi [0, 0, 1]. Setelah beberapa event berikutnya, P1 memiliki nilai [2, 2, 0] dan P2 menjadi [2, 2, 2]. Perubahan ini melihat Vector Clock memperbarui nilai logis berdasarkan event yang terjadi dan informasi yang diterima dari proses lain. Hasil perbandingan menunjukkan VC0 terjadi sebelum VC1, sedangkan VC1 tidak terjadi sebelum VC0. VC0 dan VC2 juga tidak termasuk event concurrent karena terdapat hubungan kausal di antara keduanya. Dibandingkan perhitungan manual, penggunaan program Python mempermudah pembaruan dan perbandingan nilai vektor serta mengurangi risiko kesalahan perhitungan.

<br></br>

<h3>3.3 Problem Tanpa Sinkronisasi</h3>

<i>multithreaded-example.py</i>
```bash
import threading
import time

def task(name, delay):
    print(f"Task {name} starting...")
    time.sleep(delay)  # Simulates an I/O-bound operation
    print(f"Task {name} finished after {delay}s.")

# 1. Create thread objects
t1 = threading.Thread(target=task, args=("A", 5))
t2 = threading.Thread(target=task, args=("B", 5))
t3 = threading.Thread(target=task, args=("C", 5))
t4 = threading.Thread(target=task, args=("D", 5))
t5 = threading.Thread(target=task, args=("E", 5))

# 2. Start the threads
t1.start()
t2.start()
t3.start()
t4.start()
t5.start()

# 3. Wait for threads to finish before continuing
t1.join()
t2.join()
t3.join()
t4.join()
t5.join()

print("All tasks completed.")
```
**Output pertama :**
```bash
Task A starting...
Task B starting...
Task C starting...
Task D starting...
Task E starting...
Task A finished after 5s.
Task B finished after 5s.
Task C finished after 5s.
Task D finished after 5s.
Task E finished after 5s.
All tasks completed.
```
**Output kedua :**
```bash
Task A starting...
Task B starting...
Task C starting...
Task D starting...Task E starting...

Task A finished after 5s.
Task B finished after 5s.
Task C finished after 5s.
Task D finished after 5s.Task E finished after 5s.

All tasks completed.
```
pembahasan :

Berdasarkan dua kali eksekusi program multithreading, seluruh task A sampai E berhasil dijalankan dan diselesaikan. Urutan pesan mulai dan selesai pada kedua output tetap sama, yaitu A, B, C, D, dan E. Meskipun multithreading memungkinkan beberapa thread berjalan secara bersamaan, urutan output yang berbeda tidak selalu muncul pada setiap eksekusi. Ini menunjukkan penjadwalan thread menghasilkan urutan keluaran yang sama.

<br></br>

**3.3.1 Data Race / Race Conditions**

<i>race-conditions-01.py</i>
```bash
import time
from threading import Thread

balance = 100

def withdraw(amount):
    global balance
    if balance >= amount:
        time.sleep(0.01)
        balance -= amount
        print(f"Mengambil sejumlah Rp {amount}. Saldo: Rp {balance}")

t1 = Thread(target=withdraw, args=(80,))
t2 = Thread(target=withdraw, args=(80,))

t1.start(); t2.start()
t1.join(); t2.join()
```
**Output :**
```bash
Mengambil sejumlah Rp 80. Saldo: Rp 20
Mengambil sejumlah Rp 80. Saldo: Rp -60
```
pembahasan :

Output yang dikirim memperlihatkan dua penarikan Rp80 sama-sama berhasil dari saldo awal Rp100. Penarikan pertama menampilkan saldo Rp20 dan penarikan kedua menampilkan saldo Rp-60. Ini terjadi karena kedua thread dapat memeriksa saldo yang sama sebelum perubahan saldo selesai dilakukan. Akibatnya, kondisi saldo mencukupi tidak melindungi operasi penarikan sebagai satu kesatuan.<br></br>

<i>race-conditions-02.py</i>
```bash
import time
from threading import Thread
import threading

counter_lock = threading.Lock()
balance = 100

def withdraw(amount):
    global balance
    with counter_lock:
        if balance >= amount:
            time.sleep(0.01)
            balance -= amount
            print(f"Mengambil sejumlah Rp {amount}. Saldo: Rp {balance}")

t1 = Thread(target=withdraw, args=(80,))
t2 = Thread(target=withdraw, args=(80,))

t1.start(); t2.start()
t1.join(); t2.join()
```
**Output :**
```bash
Mengambil sejumlah Rp 80. Saldo: Rp 20
```
pembahasan :

Pada kondisi 2, hanya satu penarikan Rp80 yang berhasil dan saldo menjadi Rp20. Thread lain baru memasuki bagian kritis setelah lock dilepas, lalu melihat saldo tidak cukup. Lock membuat pemeriksaan dan pengurangan saldo dilakukan secara bergantian sehingga saldo tidak menjadi negatif.

<br></br>

**3.3.2 Deadlock**

<i>deadlock-01.py</i>
```bash
import threading
import time

# Create two locks
lock_a = threading.Lock()
lock_b = threading.Lock()

def thread_task1():
    with lock_a:
        print("Thread 1: Locked A, waiting for B...")
        time.sleep(1) # Increase chance of deadlock
        with lock_b:
            print("Thread 1: Locked both")

def thread_task2():
    with lock_b:
        print("Thread 2: Locked B, waiting for A...")
        time.sleep(1) # Increase chance of deadlock
        with lock_a:
            print("Thread 2: Locked both")

# Start threads
t1 = threading.Thread(target=thread_task1)
t2 = threading.Thread(target=thread_task2)
t1.start()
t2.start()
t1.join()
t2.join()
```
**Output :**
```bash
Thread 1: Locked A, waiting for B...
Thread 2: Locked B, waiting for A...
```
pembahasan :

Pada deadlock-01.py, terminal menunjukkan Thread 1 mengunci A dan menunggu B, sementara Thread 2 mengunci B dan menunggu A. Keduanya tidak mencapai pesan “Locked both”. Ini menunjukkan circular wait: setiap thread memegang lock yang diperlukan thread lain.<br></br>

<i>deadlock-02.py</i>
```bash
import threading
import time

# Create two shared locks
lock_a = threading.Lock()
lock_b = threading.Lock()

def thread1_task():
    if lock_a.acquire(timeout=5):
        try:
            print("Thread 1: Acquired Lock A")
            time.sleep(1)  # Simulate some work
            print("Thread 1: Waiting for Lock B...")
        finally:
            lock_a.release()
        if lock_b.acquire(timeout=5):
            try:
                print("Thread 1: Acquired Lock B")
            finally:
                lock_b.release()

def thread2_task():
    if lock_b.acquire(timeout=5):
        try:
            print("Thread 2: Acquired Lock B")
            time.sleep(1)  # Simulate some work
            print("Thread 2: Waiting for Lock A...")
        finally:
            lock_b.release()
        if lock_a.acquire(timeout=5):
            try:
                print("Thread 2: Acquired Lock A")
            finally:
                lock_a.release()

t1 = threading.Thread(target=thread1_task)
t2 = threading.Thread(target=thread2_task)

t1.start()
t2.start()
t1.join()
t2.join()
print("Execution Finished")
```

**Output :**
```bash
Thread 1: Acquired Lock A
Thread 2: Acquired Lock B
Thread 1: Waiting for Lock B...
Thread 2: Waiting for Lock A...
Thread 2: Acquired Lock AThread 1: Acquired Lock B

Execution Finished
```
pembahasan :

Pada deadlock-02.py, output ini menunjukkan thread dapat memperoleh lock berikutnya dan program berakhir dengan “Execution Finished”, sehingga tidak terjadi penantian permanen pada eksekusi yang ditampilkan.

<br></br>

<h3>3.4 Algoritma Raft</h3>

<i>raft.py</i>
```bash
import random
import time
from enum import Enum
from typing import List, Dict, Optional

class State(Enum):
    FOLLOWER = 1
    CANDIDATE = 2
    LEADER = 3

class RaftNode:
    def __init__(self, node_id: int, all_nodes: List[int]):
        self.id = node_id
        self.nodes = all_nodes
        self.state = State.FOLLOWER
        self.current_term = 0
        self.voted_for: Optional[int] = None
        self.log: List[str] = []
        self.commit_index = 0
        self.last_heartbeat = time.time()
        self.election_timeout = self._random_timeout()

    def _random_timeout(self) -> float:
        return time.time() + random.uniform(1.0, 2.0)

    def on_heartbeat(self, term: int):
        if term >= self.current_term:
            self.current_term = term
            self.state = State.FOLLOWER
            self.voted_for = None
            self.last_heartbeat = time.time()
            self.election_timeout = self._random_timeout()

    def start_election(self):
        self.current_term += 1
        self.state = State.CANDIDATE
        self.voted_for = self.id
        votes = 1  # vote for self

        # Simulate requesting votes from others
        for node_id in self.nodes:
            if node_id == self.id:
                continue
            # In real Raft, we'd send RequestVote RPC
            # Here, we simulate: grant vote if term is higher and log is not behind
            votes += 1  # Simplified: assume all grant vote

        if votes > len(self.nodes) // 2:
            self.state = State.LEADER
            print(f"Node {self.id} elected leader in term {self.current_term}")

    def append_entry(self, entry: str):
        if self.state == State.LEADER:
            self.log.append(entry)
            print(f"Leader {self.id} appended: {entry}")
            # In real system, replicate to followers
            self.commit_index = len(self.log) - 1

    def tick(self):
        now = time.time()
        if self.state == State.LEADER:
            # Send heartbeat (simplified)
            pass
        elif now > self.election_timeout:
            self.start_election()
        elif self.state == State.FOLLOWER and now - self.last_heartbeat > 2.0:
            # Missed heartbeats → start election
            self.election_timeout = self._random_timeout()
            self.start_election()


def simulate_raft():
    node_ids = [1, 2, 3]
    nodes = [RaftNode(i, node_ids) for i in node_ids]

    # Simulate time steps
    for step in range(20):
        time.sleep(0.5)
        print(f"\n--- Step {step + 1} ---")

        # Randomly trigger heartbeat from current leader (if any)
        leaders = [n for n in nodes if n.state == State.LEADER]
        if leaders:
            leader = random.choice(leaders)
            for node in nodes:
                if node.id != leader.id:
                    node.on_heartbeat(leader.current_term)
            # Leader appends a command every few steps
            if step % 5 == 0:
                leader.append_entry(f"command-{step}")

        # Each node processes its state
        for node in nodes:
            node.tick()

        # Print status
        for node in nodes:
            print(f"Node {node.id}: {node.state.name} | Term {node.current_term} | Log len {len(node.log)}")


if __name__ == "__main__":
    print(" Simulating Raft Consensus for Drone Swarm Coordination\n")
    simulate_raft()
```

**Output :**
```bash
 Simulating Raft Consensus for Drone Swarm Coordination


--- Step 1 ---
Node 1: FOLLOWER | Term 0 | Log len 0
Node 2: FOLLOWER | Term 0 | Log len 0
Node 3: FOLLOWER | Term 0 | Log len 0

--- Step 2 ---
Node 1: FOLLOWER | Term 0 | Log len 0
Node 2: FOLLOWER | Term 0 | Log len 0
Node 3: FOLLOWER | Term 0 | Log len 0

--- Step 3 ---
Node 1: FOLLOWER | Term 0 | Log len 0
Node 2: FOLLOWER | Term 0 | Log len 0
Node 3: FOLLOWER | Term 0 | Log len 0

--- Step 4 ---
Node 1 elected leader in term 1
Node 2 elected leader in term 1
Node 3 elected leader in term 1
Node 1: LEADER | Term 1 | Log len 0
Node 2: LEADER | Term 1 | Log len 0
Node 3: LEADER | Term 1 | Log len 0

--- Step 5 ---
Node 1: FOLLOWER | Term 1 | Log len 0
Node 2: FOLLOWER | Term 1 | Log len 0
Node 3: LEADER | Term 1 | Log len 0

--- Step 6 ---
Leader 3 appended: command-5
Node 1: FOLLOWER | Term 1 | Log len 0
Node 2: FOLLOWER | Term 1 | Log len 0
Node 3: LEADER | Term 1 | Log len 1

--- Step 7 ---
Node 1: FOLLOWER | Term 1 | Log len 0
Node 2: FOLLOWER | Term 1 | Log len 0
Node 3: LEADER | Term 1 | Log len 1

--- Step 8 ---
Node 1: FOLLOWER | Term 1 | Log len 0
Node 2: FOLLOWER | Term 1 | Log len 0
Node 3: LEADER | Term 1 | Log len 1

--- Step 9 ---
Node 1: FOLLOWER | Term 1 | Log len 0
Node 2: FOLLOWER | Term 1 | Log len 0
Node 3: LEADER | Term 1 | Log len 1

--- Step 10 ---
Node 1: FOLLOWER | Term 1 | Log len 0
Node 2: FOLLOWER | Term 1 | Log len 0
Node 3: LEADER | Term 1 | Log len 1

--- Step 11 ---
Leader 3 appended: command-10
Node 1: FOLLOWER | Term 1 | Log len 0
Node 2: FOLLOWER | Term 1 | Log len 0
Node 3: LEADER | Term 1 | Log len 2

--- Step 12 ---
Node 1: FOLLOWER | Term 1 | Log len 0
Node 2: FOLLOWER | Term 1 | Log len 0
Node 3: LEADER | Term 1 | Log len 2

--- Step 13 ---
Node 1: FOLLOWER | Term 1 | Log len 0
Node 2: FOLLOWER | Term 1 | Log len 0
Node 3: LEADER | Term 1 | Log len 2

--- Step 14 ---
Node 1: FOLLOWER | Term 1 | Log len 0
Node 2: FOLLOWER | Term 1 | Log len 0
Node 3: LEADER | Term 1 | Log len 2

--- Step 15 ---
Node 1: FOLLOWER | Term 1 | Log len 0
Node 2: FOLLOWER | Term 1 | Log len 0
Node 3: LEADER | Term 1 | Log len 2

--- Step 16 ---
Leader 3 appended: command-15
Node 1: FOLLOWER | Term 1 | Log len 0
Node 2: FOLLOWER | Term 1 | Log len 0
Node 3: LEADER | Term 1 | Log len 3

--- Step 17 ---
Node 1: FOLLOWER | Term 1 | Log len 0
Node 2: FOLLOWER | Term 1 | Log len 0
Node 3: LEADER | Term 1 | Log len 3

--- Step 18 ---
Node 1: FOLLOWER | Term 1 | Log len 0
Node 2: FOLLOWER | Term 1 | Log len 0
Node 3: LEADER | Term 1 | Log len 3

--- Step 19 ---
Node 1: FOLLOWER | Term 1 | Log len 0
Node 2: FOLLOWER | Term 1 | Log len 0
Node 3: LEADER | Term 1 | Log len 3

--- Step 20 ---
Node 1: FOLLOWER | Term 1 | Log len 0
Node 2: FOLLOWER | Term 1 | Log len 0
Node 3: LEADER | Term 1 | Log len 3
```
Pembahasan :

Output dimulai dengan ketiga node sebagai FOLLOWER pada term 0. Pada Step 3, Node 3 terpilih menjadi LEADER pada term 1. Node 1 dan Node 2 tetap FOLLOWER dan kemudian memperbarui term menjadi 1 setelah menerima informasi leader. Pada Step 6, leader menambahkan command-5; pada Step 11 command-10; dan pada Step 16 command-15. Panjang log Node 3 bertambah menjadi 1, 2, lalu 3. Pada output yang dikirim, entri log tersebut tampak pada leader saja; jangan menyimpulkan bahwa replikasi ke follower telah berhasil jika output tidak menunjukkannya.
