# Fault-Tolerant Distributed Object Storage — Hackathon Project

Simple demo jo dikhata hai: **replication (3 copies)**, **node failure handling**,
**corruption detection (checksum)**, aur **automatic repair** — bina kisi bhaari
database/library ke, sirf Node.js + Express.

---

## Kaise chalayein (Setup)

### 1. Node.js install hona chahiye
Check karo terminal mein:
```
node -v
npm -v
```
Agar nahi hai, [nodejs.org](https://nodejs.org) se install kar lo (LTS version).

### 2. Dependencies install karo
Project folder ke andar jaake ye chalao (internet chahiye, ek baar hi karna hai):
```
npm install
```

### 3. Sab kuch ek saath chalao
```
npm run start-all
```
Ye ek command 4 cheeze ek saath start kar dega:
- `node1` → port 3001
- `node2` → port 3002
- `node3` → port 3003
- `coordinator` → port 4000

Terminal mein 4 alag-alag colour mein logs dikhenge — sabka status "running" aana chahiye.

### 4. Dashboard kholo
Browser mein jaao:
```
http://localhost:4000
```

Agar `npm run start-all` kaam na kare (kabhi-kabhi Windows pe dikkat aati hai), toh **4 alag terminal
windows** kholke ek-ek command alag mein chalao:
```
npm run node1
npm run node2
npm run node3
npm run coordinator
```

---

## Kaise use karein / Demo kaise dena hai

1. **Upload**: koi bhi file (photo, pdf, txt) choose karo aur "Upload" dabao.
   Dashboard pe dikhega ki file kaunse-kaunse nodes pe gayi.

2. **Node crash simulate karo**: jis terminal mein `node2` chal raha hai usme
   `Ctrl+C` dabao. Dashboard pe node2 turant "🔴 DOWN" ho jaayega
   (3 second ke andar). File abhi bhi download ho rahi hogi — baaki 2 nodes se.

3. **Node wapas laao**: usi terminal mein `npm run node2` chala do.
   Kuch second ke andar dashboard pe node2 "🟢 UP" ho jaayega, aur
   **automatic repair** khud us node pe file wapas copy kar dega
   (terminal mein `[REPAIR] Restored missing copy...` log dikhega).

4. **Corruption simulate karo**: kisi healthy replica ke saamne "Corrupt this copy"
   button dabao. Wo copy turant "corrupted" dikhne lagegi (checksum mismatch).
   5 second ke andar background repair job khud usse theek kar dega —
   dashboard refresh hoke "healthy" wapas dikha dega.

5. **Download**: kisi bhi file ke saamne "Download" dabao — system khud decide
   karega kis healthy node se file laani hai.

---

## Architecture (1-line summary for judges)

```
Browser Dashboard → Coordinator (replication + health-check + auto-repair)
                          ↓         ↓          ↓
                      Node1      Node2      Node3
                    (storage)  (storage)  (storage)
```

- Har file **3 replicas** mein store hoti hai (fault tolerance)
- Har chunk ka **SHA-256 checksum** store hota hai (corruption detection)
- Coordinator har **5 second mein background check** chalata hai:
  - Node down hai? → skip kar do, jab wapas aaye tab repair karo
  - File corrupt hai kisi node pe? → doosre healthy node se copy karke fix karo
  - File missing hai kisi node pe? → doosre healthy node se restore karo
- Download hamesha **kisi bhi available healthy replica** se milta hai — single
  point of failure nahi hai

---

## File Structure
```
project/
├── storageNode.js      → ek storage node ka code (3 baar chalta hai, alag ports pe)
├── coordinator.js       → replication + repair + API layer
├── public/
│   ├── index.html        → dashboard UI
│   ├── style.css          → styling
│   └── app.js               → dashboard logic (upload/download/status)
├── package.json
└── README.md (ye file)
```

## Agar Presentation/PPT mein dikhana ho
- "Real systems jaise Amazon S3, Google Cloud Storage bhi isi tarah kaam karte
  hain — data multiple machines pe replicate hota hai, aur agar ek machine
  fail ho jaaye ya data corrupt ho jaaye, system khud detect karke repair kar
  leta hai, user ko pata bhi nahi chalta."
- Live demo mein node crash + corruption dono dikhana — judges isko sabse zyada
  appreciate karte hain kyunki "fault tolerance" ka concept **actually kaam
  karta hua** dikh raha hota hai, sirf slides pe nahi.

## Future Scope (agar poochein "aur kya add kar sakte ho")
- Erasure coding (kam storage overhead ke saath fault tolerance)
- Multiple metadata replicas (Raft consensus) — abhi single in-memory hai
- Bigger cluster (10+ nodes) aur consistent hashing based placement
- Partition tolerance (network split scenarios)
