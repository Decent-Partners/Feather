### Feather Platform MVP Product Outline (British English)

#### 1. Executive Summary
The Feather Platform is a decentralised, on-chain social curation tool. It is built on the open-source Feather Protocol on Kusama Asset Hub and enables users to publish, curate, and network around short-form ideas, art, and inspirations called **Feathers**. Inspired by Are.na’s minimalist exploration of interconnected blocks and channels, Feather transforms raw user submissions (via Kusama’s `system.remarkWithEvent`) into evolving, community-curated **Zines** - themed collections (Decent / FTTF etc) that function as lightweight Kreivo DAOs with optional membership. The MVP focuses on core loops: **post**, **curate**, and **network**.

This is a permissionless, experimental platform amplifying Kusama’s chaotic creativity - no gatekeepers, just emergent culture. The MVP will be a web app with a clean, text-first UI (SvelteKit frontend), integrating the Feather Index for querying on-chain data. Target users: Kusama builders, artists, and memers seeking a lightweight alternative to bloated social media.

**MVP Scope**: Elegant, clean, single-page app with Virto Connect wallet login, posting form, Zine curation dashboard, and basic feeds. No advanced ZKPs, DAO governance, or validator media rendering in MVP - focus on core protocol integration. 

#### 2. Inspiration and High-Level Vision
- **Are.na Explorer Influence**: Are.na’s “Explore” is a serene, grid-based discovery hub where users browse interconnected “blocks” (text, images, links) organised into “channels” (themed collections). UX is deliberately simple: infinite scroll grids, hover previews, and subtle connections via embeds/links. No algorithms, just human-curated serendipity. Feather mirrors this with on-chain Feathers as atomic blocks, Zines as public channels, and a text-minimalist design (monospace fonts, subtle animations, dark/light modes).
- **Other Inspirations**: Twitter’s remark-like brevity meets Tumblr’s zine aesthetics; on-chain permanence but for prose/poetry/logs/clips/images/links.
- **Platform Ethos**: “Create ChaOS”—amplify Kusama’s raw, unfiltered energy. Feathers are ephemeral yet eternal (on-chain), Zines evolve via curation (off-chain for MVP).

#### 3. Target Users and Pain Points
- **Primary Users**: 
  - Kusama ecosystem participants (validators, developers, artists, contributors) wanting to log atomic ideas in a stream of conscious manner that can then be networked together into emergent knowledge graphs without centralised platforms or intermediaries. 
  - Creatives seeking inspiration: Poets, theorists, log-keepers. .
- **Pain Points Addressed**:
  - No native Kusama tool for diverse, on-chain expression (beyond transactions).
  - Spam/offensive content risks: Mitigated via variable submission fees ($0.01 -> $0.1) to be paid in dUSD.  
  - Scalability: Off-chain indexing + IPFS for media keeps it lightweight.
- **User Personas**:
  - **The Dreamer**: Posts poetic “dream” Feathers; curates Zines for surreal Kusama lore.
  - **The Builder**: Logs “alert” Feathers on chain issues; networks via follows.
  - **The Curator**: Aggregates Feathers into themed Zines (e.g., “Onchain Telepathy”) for community vibes.

#### 4. Key Features
Prioritised for MVP: Post > Curate > Network. All interactions via Profile / Virto Connect. 

| Feature | Description | MVP Implementation | Post-MVP Extensions |
|---------|-------------|---------------------|---------------------|
| **Post (Feathers)** | Users publish short content as on-chain remarks. | Simple form: Genre dropdown, optional title, text/IPFS hash input. Submits via Polkadot.js to `system.remarkWithEvent` (format: `FEATHER::<genre>::<title>::<content>`). Success toast with block explorer link. Genres: theory, rant, poem, image, log, dream, scene, fiction, alert (per protocol). | ZK proofs for pseudonyms/rate limits; batching for fee efficiency. |
| **Curate (Zines)** | Aggregate Feathers into evolving collections; Zines gain “members” via follows/likes. | Dashboard: Create Zine (name, description, optional genre filter). Add Feathers via drag-select from feeds (off-chain state). Zines render as paginated grids/PDF-like views (generative layout via basic CSS). Membership: Simple “Join” button (tracks followers). | Kreivo DAO integration for on-chain governance; validator AI transcoding for layouts. |
| **Network (Discovery)** | Browse, follow, and engage with Feathers/Zines. | Infinite-scroll feed: Latest Feathers (via Index WS query: `{"type": "GetFeathers", "limit": 20}`). Filters: By genre/account. Follows: Wallet-based (store in local DB or IPFS). Profile: List of user’s Feathers/Zines. | Topical debates/threads; sentiment analysis for governance tools. |
| **Read/Explore** | Serendipitous browsing like Are.na’s grid. | Landing page: Grid of recent Zines/Feathers with hover previews (text snippets, IPFS images). Search by keyword/genre. | Mindmaps; real-time feeds via WebSockets. |

#### 5. User Flows
1. **Onboarding**:
   - Land on `/explore`: See grid of top Zines/Feathers (query Index for block 0, limit 20).
   - Click “Connect Wallet” (Polkadot.js extension prompt). No KYC—proceed to dashboard.
   
2. **Posting a Feather**:
   - Navigate to `/post`: Select genre, enter title/content (max 280 characters or IPFS CID for media).
   - Preview: Render as card (e.g., monospace text for poems).
   - Submit: Estimate fee (0.01–0.1 KSM), sign transaction, show transaction hash. Redirect to `/explore` with new Feather highlighted.

3. **Curating a Zine**:
   - From feed, click “New Zine”: Name it (e.g., “Kusama Dreams”), add initial Feathers via multi-select.
   - Edit view: Drag/drop Feathers into sequence; auto-generate layout (grid for images, linear for logs).
   - Publish: Save as public (IPFS for metadata, link to on-chain Feathers). Share URL.

4. **Networking**:
   - Browse `/explore?genre=theory`: Filter/query Index (`{"type": "GetFeathers", "genre": "theory", "limit": 50}`).
   - Follow user/Zine: Button toggles; local storage tracks (sync to IPFS later).
   - Profile `/user/<account_id>`: List Feathers/Zines; follow button.

**Edge Cases**: Offline mode (cache recent queries); fee failures (retry with gas estimate); IPFS resolution (fallback to hash display).

#### 6. Technical Architecture
- **Frontend**: SvelteKit (for SSR/simplicity, like Are.na’s clean React vibes). Monospace fonts (e.g., IBM Plex Mono), subtle colours (feather greys/blues). Responsive: Mobile-first grids.
- **Blockchain Integration**:
  - Posting: `@polkadot/api` for transaction submission to Kusama RPC (wss://kusama-rpc.polkadot.io).
  - Querying: WebSocket to local/remote Feather Index (port 8172). JSON queries (e.g., GetFeathers with filters). Poll every 30 seconds for new blocks.
- **Off-Chain**:
  - Indexing: Assume developer runs Feather Index locally or via hosted instance (Dockerise for ease). Fetches remarks, stores in SQLite.
  - Storage: IPFS (via nft.storage or Pinata) for images/media; on-chain only for hashes.
  - State: LocalStorage for follows/Zine drafts; IPFS for published Zines (CID linking to Feather IDs).
- **Security/Privacy**: Wallet-only authentication. No user data stored server-side. Post-MVP: Noir ZK for anonymous posts.
- **Deployment**: Vercel/Netlify for frontend; self-host Index on VPS. No backend needed for MVP.

#### 7. UX/UI Guidelines
- **Simplicity First**: Are.na-inspired minimalism—no adverts, infinite scrolls without overload. 3-column navigation: Explore | Post | Profile.
- **Visual Style**: 
  - Cards: Borderless, subtle shadows; text left-aligned, images full-bleed.
  - Animations: Fade-ins for new Feathers; typewriter effect for poems.
  - Accessibility: High contrast, keyboard navigation, alt text for IPFS images.
- **Onboarding Nudge**: Empty state: “Publish your first Feather: What Kusama dream keeps you up at night?”
- **Mobile**: Stacked cards; swipe to curate.

#### 8. Success Metrics and Iteration
- **MVP KPIs**: 100+ Feathers posted in week 1; 10+ Zines created; 50 DAU via wallet connects.
- **Analytics**: On-chain (feather counts via Index)
  - Chain Congestion: Fee warnings; batching hints.
  - Spam: Genre filters; community flagging (manual for MVP).
- **Next Phases**: Integrate Kreivo for Zine DAOs; add voice/video Feathers; mobile app.


