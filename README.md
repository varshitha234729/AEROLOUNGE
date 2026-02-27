#✈ AeroLounge — Biometric Entry System

A premium airport lounge access system using real-time face recognition — fully browser-based, zero server required.

---

## 🧠 Concept

AeroLounge replaces physical membership cards with biometric face scanning at the gate. Staff enroll members once via webcam or photo upload; from then on, members walk up to the scanner and gain instant access — no cards, no queues, no friction.

The system runs entirely in the browser using `localStorage` for data persistence and the `Canvas API` for image processing, making it deployable as a simple static site with no backend infrastructure.

---

## 📁 Project Structure

```
aerolounge/
├── index.html              # Landing page / dashboard
├── live-recognition.html   # Gate camera & face matching engine
└── member-management.html  # Member CRUD + face enrollment
```

---

## 🖥 Pages

### `index.html` — Dashboard
The marketing/home page with an animated splash screen, hero section, feature highlights, and a "How It Works" walkthrough. Pulls live member/face counts from `localStorage` to display real stats.

### `live-recognition.html` — Live Entry
The operator-facing gate terminal. Features:
- Live webcam feed with scan-line overlay
- One-click **CAPTURE & SCAN** to run face matching
- Confidence score display with animated progress bar
- **ACCESS GRANTED / DENIED** modal with auto-dismiss
- Real-time access log with member thumbnails
- Session stats (granted, denied, total scans, match rate %)
- Manual gate override buttons for supervisors

### `member-management.html` — Member Database
The admin panel for managing the member roster. Features:
- Register new members with name, tier, lounge access, expiry date
- Enroll face via live webcam capture or photo file upload
- Table view with search and tier filter
- Per-member face re-enrollment and deletion
- Stats strip: total members, enrolled faces, platinum tier count, missing faces
- CSV export

---

## ⚙️ How the Face Matching Works

The system uses a **colour histogram cosine similarity** approach as a browser-side proxy for deep face embeddings:

1. Both the captured frame and the stored face image are drawn onto a 64×64 `<canvas>`
2. A 48-bin RGB histogram is extracted (16 bins per channel)
3. Histograms are normalised and compared using **cosine similarity**
4. A score ≥ `0.72` is treated as a match

This approach is entirely client-side (no API calls, no ML model downloads) and works reliably when the same webcam and lighting conditions are used for enrollment and scanning — exactly the real-world lounge gate scenario.

> In a production deployment, this layer would be replaced by a proper face embedding model (e.g. DeepFace, FaceNet, or a cloud vision API) while keeping the rest of the UI and data flow identical.

---

## 💾 Data Storage

All data lives in the browser's `localStorage` under two keys:

| Key | Contents |
|---|---|
| `aerolounge_members` | JSON array of member records (name, tier, lounge, expiry…) |
| `aerolounge_faces` | JSON object mapping `member_id → base64 JPEG data URL` |

Both pages read from and write to the same keys, so enrolling a face on the Database page is immediately available to the Live Entry scanner — no sync step needed.

---

## 🚀 Getting Started

Because webcam access requires a secure context, you **cannot** open the HTML files directly from the filesystem (`file://`). Run a local server instead:

**Python 3**
```bash
cd path/to/aerolounge
python -m http.server 8080
```
Then open `http://localhost:8080` in your browser.

**Node.js (npx)**
```bash
npx serve .
```

**VS Code**
Use the [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) extension and click "Go Live".

---

## 🛠 Tech Stack

| Layer | Technology |
|---|---|
| UI Framework | Vanilla HTML/CSS/JS — no build step |
| Fonts | Bebas Neue, DM Sans, JetBrains Mono (Google Fonts) |
| Camera | `getUserMedia` Web API |
| Image Processing | Canvas 2D API |
| Face Matching | Colour histogram cosine similarity |
| Storage | Browser `localStorage` |
| Styling | Custom CSS with CSS variables, glassmorphism, animations |

---

## 🎨 Design System

| Token | Value | Usage |
|---|---|---|
| `--bg` | `#05090f` | Page background |
| `--accent` | `#00dcb4` | Primary teal — success, active states |
| `--accent2` | `#0072ff` | Blue — secondary highlights |
| `--danger` | `#ff4060` | Red — denied, errors |
| `--text` | `#e8f0ff` | Body text |
| `--muted` | `#556070` | Labels, secondary text |

UI components use backdrop blur, subtle grid overlays, and ambient orb gradients to achieve a premium terminal aesthetic.

---

## 👥 Demo Members

Three members are seeded automatically on first load (if `localStorage` is empty):

| ID | Name | Tier | Lounge |
|---|---|---|---|
| PM001 | Arjun Mehta | Platinum | Gate 7B |
| PM002 | Priya Sharma | Gold | Gate 3A |
| PM003 | Vikram Kapoor | Platinum | All Lounges |

Their face data is not pre-enrolled — enroll faces via the Database page to enable scanning.

---

## 🔒 Privacy & Security Notes

- **No data leaves the device.** All face images are stored as base64 strings in `localStorage`.
- Face images are never transmitted to any server.
- Clearing browser storage (`localStorage.clear()`) permanently removes all member and face data.
- For a real deployment, replace `localStorage` with an encrypted server-side store and the histogram matcher with a proper biometric model behind an authenticated API.

---

## 📄 License

MIT — free to use, modify, and deploy.

---

*AeroLounge Biometric Entry System · 2026*
