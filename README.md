# BidEasy

College project (PS025). Real-time auction floor: Next.js frontend and Spring Boot microservices. Prices are **INR**, stored as integer paise.

| Part | Folder | GitHub |
| --- | --- | --- |
| Frontend | `soa-bidding` | https://github.com/dhanushyangal/soa_frountend |
| Backend | `soa-bidding-backend` | https://github.com/dhanushyangal/Soa_backand |

These are two separate git repositories. Clone them side by side, then start the **backend first** and the frontend second. Use two terminals.

```bash
git clone https://github.com/dhanushyangal/Soa_backand.git soa-bidding-backend
git clone https://github.com/dhanushyangal/soa_frountend.git soa-bidding
```

Run the `cd` commands below from the folder that contains both clones. If you already opened only one repo, skip that repo’s `cd`.

---

## What you need

- **Java 17 or 21** (`java -version`)
- **Python 3** (used by `start-all.sh`)
- **Node.js 20+** and **npm**
- A filled backend `.env` and frontend `.env.local` (copy from each `.env.example`)
- Free ports **3000, 8761, 8080, 8081, 8082, 8083, 8084**

On a Mac with Homebrew OpenJDK, `java` is often not on `PATH` until you add it:

```bash
export PATH="/opt/homebrew/opt/openjdk@21/bin:$PATH"
export JAVA_HOME="/opt/homebrew/opt/openjdk@21/libexec/openjdk.jdk/Contents/Home"
java -version
```

---

## 1. Backend

```bash
cd soa-bidding-backend
cp .env.example .env
```

Fill `.env` before starting. Required:

```
SUPABASE_DB_URL=jdbc:postgresql://YOUR_HOST:5432/postgres?sslmode=require
SUPABASE_DB_USERNAME=
SUPABASE_DB_PASSWORD=
CLERK_ISSUER=https://YOUR_CLERK_APP.clerk.accounts.dev
CLERK_JWKS_URL=https://YOUR_CLERK_APP.clerk.accounts.dev/.well-known/jwks.json
INTERNAL_SERVICE_TOKEN=dev-internal-token
CORS_ORIGIN=http://localhost:3000
EUREKA_URL=http://localhost:8761/eureka/
```

Apply `soa-bidding-backend/schema.sql` once in the Supabase SQL editor if the tables are not there yet.

Start all six services (builds JARs if they are missing):

```bash
chmod +x start-all.sh stop-all.sh
./start-all.sh
```

| Service | URL |
| --- | --- |
| Eureka | http://localhost:8761 |
| API gateway | http://localhost:8080 |
| Auth | http://localhost:8081 |
| Auction | http://localhost:8082 |
| Bidding | http://localhost:8083 |
| Payment | http://localhost:8084 |

Check:

```bash
curl http://localhost:8080/api/auctions
```

Logs: `soa-bidding-backend/logs/*.log`

Stop:

```bash
./stop-all.sh
```

Do not run `java -jar` by itself. The database URL comes from `.env`, and a bare `java -jar` does not load that file. `./start-all.sh` loads it.

Wallet top-up also needs the Dodo keys in `.env`. Bidding works without them if the wallet already has a balance.

---

## 2. Frontend

```bash
cd soa-bidding
cp .env.example .env.local
npm install
npm run dev
```

Fill `.env.local`. Use the **same Clerk application** as `CLERK_ISSUER` on the backend.

```
NEXT_PUBLIC_API_URL=http://localhost:8080
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_test_...
CLERK_SECRET_KEY=sk_test_...
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in
NEXT_PUBLIC_CLERK_SIGN_UP_URL=/sign-up
NEXT_PUBLIC_CLERK_SIGN_IN_FALLBACK_REDIRECT_URL=/marketplace
NEXT_PUBLIC_CLERK_SIGN_UP_FALLBACK_REDIRECT_URL=/marketplace
NEXT_PUBLIC_CLERK_SIGN_IN_FORCE_REDIRECT_URL=/marketplace
NEXT_PUBLIC_CLERK_SIGN_UP_FORCE_REDIRECT_URL=/marketplace
NEXT_PUBLIC_SUPABASE_URL=https://YOUR_PROJECT.supabase.co
NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY=sb_publishable_...
```

Open **http://localhost:3000**. After sign-in you land on `/marketplace`.

If the UI looks stale:

```bash
npm run clean
npm run dev
```

Then hard-reload the browser (`Cmd+Shift+R`).

---

## Do not commit

- `soa-bidding-backend/.env`
- `soa-bidding/.env.local`

Listing photos are a public image URL pasted on the sell form. The app does not upload image files.
