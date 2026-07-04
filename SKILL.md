---
name: deployer-skill-mlk
description: Scaffolds a Next.js app, implements custom UI/logic, configures a CNAME file at the root, sets up GitHub Actions deployment to the gh-pages branch, and pushes to remote.
---

# Next.js App Creation & GitHub Pages Deployment Skill

## 🎯 Interactive Demonstration Protocol (Antigravity 2.0 Features Showcase)

This skill is designed for live hands-on demonstrations of the **Google Antigravity 2.0** desktop app. The executing agent **MUST** actively use both the **Subagents** panel and the **Background Tasks** panel:

### 1. Pre-Flight Protocol
Before modifying any files in the codebase, the agent **MUST**:
1. **Ask Clarifying Questions**: Ask the user to provide:
   - Target custom CNAME domain URL (e.g. `your-app.parot.dev`)
   - Target GitHub repository URL (e.g. `https://github.com/username/repo.git`)
   - Optional tech stack preferences or design assets.
2. **Draft the Implementation Plan**: Create the `implementation_plan.md` artifact in the brain folder outlining the architecture, Stripe/Linear layout style, and subagent delegation.
3. **Wait for Approval**: Set `request_feedback: true` in the plan's metadata to display the "Proceed" button in the UI. Stop and wait for the user to approve the plan.

### 2. Mandatory Background Tasks Integration
You are strictly forbidden from running long-running terminal commands synchronously in the main thread.
- **Rule**: You **MUST** launch all commands (e.g., scaffolding, dependency installs, local builds, and git pushes) asynchronously by calling the `run_command` tool with the `WaitMsBeforeAsync` parameter set to `1000` (or less) so that the user sees them active in the **Background Tasks** sidebar panel.

### 3. Mandatory Subagents Delegation
You are strictly forbidden from writing all application code yourself in the main thread.
- **Rule**: You **MUST** delegate distinct feature implementations to specialized subagents spawned via the `invoke_subagent` tool. This ensures the user sees a list of active collaborators in the **Subagents** panel. Allocate roles as follows:
  - **Content Researcher (Type: `research`)**: Scrape and query specifications and JSON schemas for the subject.
  - **UI Designer (Type: `self`)**: Refactor global CSS styles (`src/app/globals.css`) and design HSL variables.
  - **UX Specialist (Type: `self`)**: Implement interactive components and telemetry dashboards in `src/components/`.
  - **QA Analyst (Type: `self`)**: Run compilation cycles in the background to verify typescript safety.

---

## 🛠️ Step-by-Step Instructions

### Step 1: Scaffold Next.js Application (Background Task)
Since the workspace already contains `.git` and `.agents` folders, initializing directly in the root will fail.
1. Create a temporary folder named `_temp_scaffold`.
2. **Launch as Background Task**: Run the following command with `WaitMsBeforeAsync` set to `1000` or less:
   ```bash
   npx create-next-app@latest _temp_scaffold --typescript --eslint --app --src-dir --import-alias "@/*" --use-npm --disable-git --empty --yes
   ```
3. Once the background task finishes, move all files (including hidden ones) from `_temp_scaffold` to the workspace root.
4. Delete the empty `_temp_scaffold` directory.
5. **Launch as Background Task**: Run `npm install` in the background (using `WaitMsBeforeAsync` set to `1000` or less) to link all dependencies.

### Step 2: Configure Next.js for Static Export
GitHub Pages serves static sites. Configure Next.js to build and output static HTML files:
1. Locate `next.config.ts` or `next.config.mjs` in the root.
2. Edit it to add `output: 'export'` and `images: { unoptimized: true }`.
   Example `next.config.ts`:
   ```typescript
   import type { NextConfig } from "next";

   const nextConfig: NextConfig = {
     output: "export",
     images: {
       unoptimized: true,
     },
   };

   export default nextConfig;
   ```

### Step 3: Configure Custom CNAME
To point GitHub Pages to your custom CNAME:
1. Create a file at `CNAME` directly in the root folder of your project.
2. **CRITICAL**: Do NOT duplicate this CNAME file inside the `public/` folder. A duplicate CNAME will conflict and cause GitHub Pages build errors.
3. Write the single line with your domain name:
   ```text
   <your-domain.com>
   ```

### Step 4: Configure GitHub Actions CI/CD Workflow
Create the file `.github/workflows/deploy.yml` to automatically build and deploy the application to a `gh-pages` branch using a token:
```yaml
name: Deploy Next.js to Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: "20"
          cache: npm

      - name: Restore cache
        uses: actions/cache@v4
        with:
          path: |
            .next/cache
          key: ${{ runner.os }}-nextjs-${{ hashFiles('**/package-lock.json', '**/yarn.lock') }}-${{ hashFiles('**.[jt]s', '**.[jt]sx') }}
          restore-keys: |
            ${{ runner.os }}-nextjs-${{ hashFiles('**/package-lock.json', '**/yarn.lock') }}-

      - name: Install dependencies
        run: npm ci || npm install

      - name: Build with Next.js
        run: |
          npm run build
          cp CNAME out/CNAME

      - name: Deploy to GitHub Pages Branch
        uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./out
```

### Step 5: Implement Application Logic & Design (Subagent Delegation)
1. Concurrently spawn specialized subagents via `invoke_subagent` to implement the design and interactive layouts (refer to the **Multi-Agent Orchestration Protocol** below).
2. Ensure you partition files so they work in parallel without locks.

### Step 6: Verify Build Locally (Background Task)
To verify everything compiles cleanly, do not block execution. Launch the verification command in the background:
1. **Launch as Background Task**: Run the following build command with `WaitMsBeforeAsync` set to `1000` or less:
   ```bash
   npm run build
   ```
2. Monitor progress using the `manage_task` status checks.

### Step 7: Commit and Push to Remote (Background Task)
Push the codebase to trigger the Actions deployment:
1. Stage files: `git add .`
2. Commit: `git commit -m "feat: compile and implement application based on user request"`
3. **Launch as Background Task**: Push the commits in the background with `WaitMsBeforeAsync` set to `1000` or less:
   ```bash
   git push -u origin main
   ```

---

## 💎 Premium Quality Standards & Multi-Agent Collaboration

To deliver premium-grade web applications that exceed the quality of standard AI-generated code, implement the following guidelines:

### 1. Premium Visual Aesthetics
- **Theme Selection**: If the user explicitly requests a specific visual style, design system, or theme, implement it precisely according to their instructions. If the user does not specify a style, default to a premium, hand-crafted Stripe/Linear-inspired visual layout.
- **Design Guidelines**: Use clean grid lines, subtle gradients, and glassmorphic layers. Strictly avoid typical AI-generated layout clichés (such as floating random background particles, low-contrast text on dark backgrounds, or glowing boxes without clear design purposes).
- **Typography & Font Scaling**: Use modern typography (e.g., Outfit, Inter) via `next/font/google` and size elements proportionally for excellent readability.

### 2. Dual-Theme Support (Light & Dark Modes)
- **Implementation**: The application must natively support both light and dark modes (switching classes `.light` and `.dark` dynamically on the container or body).
- **HSL Tokens**: Set up color palettes using HSL variables to adjust contrast ratios and keep text highly accessible and clear under both themes.

### 3. Multi-Agent Orchestration Protocol (Maximizing Speed & Quality)
To optimize execution speed and deliver high-fidelity applications faster, the developer agent must delegate tasks to specialized subagents in a highly parallelized, asynchronous manner:

- **Maximize Parallelism for Development Velocity**:
  - Do NOT execute tasks sequentially. Spawn multiple subagents *concurrently* (e.g. at the start of the task) to perform research, visual styling, and component building in parallel, significantly compressing wall-clock execution time.
- **No Limit on Subagent Allocations**:
  - There is no limit on the number of subagents that can be spawned to solve a problem. Spawn as many specialized, parallel subagents as required to isolate concerns and execute tasks faster (e.g. separate subagents for backend API endpoints, state management hooks, database wireups, styling modules, and unit test validations).
- **Strict Task Partitioning (Avoiding Conflicts)**:
  - Partition the workspace tasks cleanly so subagents work on isolated files, avoiding write locks or edit conflicts:
    - **Content Researcher (`research`)**: Runs in the background to scrape, query web APIs, and retrieve precise, up-to-date specs.
    - **UI Designer (`self`)**: Focuses exclusively on styling files (`src/app/globals.css`, theme variables) in parallel.
    - **UX Specialist (`self`)**: Focuses on building new interactive React components in `src/components/`.
    - **QA Analyst (`self`)**: Focuses on compiling code, running tests, and validating Next.js builds.
- **Asynchronous Synchronization**:
  - Use `inherit` workspace mode for spawned subagents so they instantly share the project filesystem.
  - Rely on the reactive wake-up messaging system instead of polling or waiting in blocking loops, keeping the main coordinator agent free to integrate assets as subagents finish their modules.
