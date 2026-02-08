# Focus Flow

**A simple, elegant work timer and todo list built with SvelteKit.**

Focus Flow helps you stay productive by combining a Pomodoro-style timer with a minimal, persistent todo list. Built for speed and simplicity, powered by SvelteKit and Tailwind CSS.

---

## Features

- ⏲️ **Work Timer**: Pomodoro-style, customizable durations
- 📝 **Todo List**: Add, complete, and persist tasks in your browser
- 💾 **Local Storage**: No account required, your data stays on your device
- ⚡ **Fast & Modern**: Built with SvelteKit, Tailwind CSS, and Bun

---

## Getting Started

### 1. Clone the repository

```sh
git clone https://github.com/yourusername/focus-flow-sv.git
cd focus-flow-sv
```

### 2. Install dependencies (with Bun)

```sh
bun install
```

### 3. Set up environment variables

Copy the example environment file and edit as needed:

```sh
cp env.sample .env
# Then edit .env if you want to customize timer defaults
```

---

## Developing

Start the development server:

```sh
bun run dev
```

Or open the app in a new browser tab:

```sh
bun run dev -- --open
```

---

## Building for Production

To create a production build:

```sh
bun run build
```

Preview the production build:

```sh
bun run preview
```

---

## Deployment

This app uses the **Netlify adapter** by default and is ready to deploy to Netlify out of the box. You can easily switch to any other SvelteKit adapter (e.g., static, Vercel, Node, etc.) by updating your configuration.

For details on changing adapters or deploying to other platforms, see the [SvelteKit adapters documentation](https://kit.svelte.dev/docs/adapters).

---

## Caveats

**Local Storage Warning:**

Focus Flow stores your timer and todo data in your browser's localStorage. While this means your data is private and never leaves your device, it also means:

- Clearing your browser data, using incognito/private mode, or certain browser updates can erase your tasks and timer settings.
- Some browsers may automatically clear localStorage under low disk space or other conditions.

If you need to keep your data long-term, consider exporting your tasks or making regular backups.

---

## License

[GLWTPL](https://github.com/me-shaon/GLWTPL)
