<script>
    import "./assets/app.css"; // Global Tailwind/Custom styles
    import { fade } from "svelte/transition";

    // Components
    import Home from "./components/pages/Home.svelte";
    import Plugins from "./components/pages/Plugins.svelte";
    import Settings from "./components/pages/Settings.svelte";

    // Icons
    import HomeIcon from "@lucide/svelte/icons/home";
    import BlocksIcon from "@lucide/svelte/icons/blocks";
    import SettingsIcon from "@lucide/svelte/icons/settings";

    // State
    let isLoading = $state(true);
    let currentView = $state("home");
    let showDebug = $state(true);
    let totalSeconds = $state(0);

    // Voice state
    let status = $state("IDLE");
    let transcriptHistory = $state([]);
    let vadEnabled = $state(false);

    let uptimeString = $derived.by(() => {
        const h = Math.floor(totalSeconds / 3600)
            .toString()
            .padStart(2, "0");
        const m = Math.floor((totalSeconds % 3600) / 60)
            .toString()
            .padStart(2, "0");
        const s = (totalSeconds % 60).toString().padStart(2, "0");

        return `${h}:${m}:${s}`;
    });

    // Logic
    import { onMount } from "svelte";
    onMount(() => {
        setTimeout(() => {
            isLoading = false;
        }, 2000);

        const interval = setInterval(() => {
            totalSeconds += 1;
        }, 1000);

        return () => clearInterval(interval); // Clean up on app close
    });
</script>

{#if isLoading}
    <div out:fade class="fixed inset-0 bg-[#050508] z-99 flex flex-col items-center justify-center">
        <div class="relative w-24 h-24 flex items-center justify-center">
            <div class="absolute inset-0 border-t-2 border-cyan-500 rounded-full animate-spin"></div>
            <div class="w-2 h-2 bg-cyan-500 rounded-full shadow-[0_0_15px_#22d3ee]"></div>
        </div>
        <span class="mt-8 font-mono text-[10px] tracking-[0.5em] text-cyan-500 animate-pulse uppercase"
            >Initializing_Atlas</span
        >
    </div>
{:else}
    <main class="h-screen w-screen bg-[#0a0a0c] text-slate-300 flex overflow-hidden font-sans selection:none">
        <nav class="w-20 z-20 border-r border-white/10 bg-[#0d0d0f] flex flex-col items-center py-6 shrink-0">
            <div class="h-16 flex items-center justify-center">
                <div
                    class="w-10 h-10 border border-cyan-500/30 rounded-xl flex items-center justify-center text-cyan-400 font-bold italic"
                >
                    A
                </div>
            </div>

            <div class="h-8"></div>

            <div class="flex flex-col gap-3 w-full px-2">
                <button
                    onclick={() => (currentView = "home")}
                    class="p-4 rounded-xl transition-all flex items-center justify-center {currentView === 'home'
                        ? 'bg-cyan-500/10 text-cyan-400 border border-cyan-500/20 shadow-[0_0_15px_rgba(34,211,238,0.1)]'
                        : 'text-slate-500 hover:text-white'}"
                >
                    <HomeIcon />
                </button>

                <button
                    onclick={() => (currentView = "plugins")}
                    class="p-4 rounded-xl transition-all flex items-center justify-center {currentView === 'plugins'
                        ? 'bg-cyan-500/10 text-cyan-400 border border-cyan-500/20 shadow-[0_0_15px_rgba(34,211,238,0.1)]'
                        : 'text-slate-500 hover:text-white'}"
                >
                    <BlocksIcon />
                </button>

                <button
                    onclick={() => (currentView = "settings")}
                    class="w-full p-4 rounded-xl transition-all flex items-center justify-center {currentView ===
                    'settings'
                        ? 'bg-cyan-500/10 text-cyan-400 border border-cyan-500/20 shadow-[0_0_15px_rgba(34,211,238,0.1)]'
                        : 'text-slate-500 hover:text-white'}"
                >
                    <SettingsIcon />
                </button>
            </div>
        </nav>

        <div class="flex-1 flex flex-col relative bg-[#0a0a0c]">
            <header class="h-16 flex items-center justify-between px-10 border-b border-white/5 bg-[#0d0d0f]/50">
                <div class="flex items-center gap-3">
                    <span class="text-[10px] font-mono uppercase tracking-[0.4em] text-cyan-500/60">Atlas</span>
                    <span class="text-[10px] text-white/20">//</span>
                    <span class="text-[10px] font-mono uppercase tracking-[0.4em] text-white/40">{currentView}</span>
                </div>

                <button
                    onclick={() => (vadEnabled = !vadEnabled)}
                    class="flex items-center gap-3 px-5 py-1.5 rounded-full border transition-all active:scale-95
        {vadEnabled
                        ? 'border-cyan-500/40 text-cyan-400 bg-cyan-500/5 shadow-[0_0_15px_rgba(34,211,238,0.1)]'
                        : 'border-red-500/40 text-red-500 bg-red-500/5'}"
                >
                    <div
                        class="w-1.5 h-1.5 rounded-full {vadEnabled ? 'bg-cyan-400 animate-pulse' : 'bg-red-500'}"
                    ></div>
                    <span class="text-[10px] font-bold uppercase tracking-[0.2em]"
                        >{vadEnabled ? "VAD Active" : "VAD Paused"}</span
                    >
                </button>
            </header>

            <div class="flex-1 overflow-y-auto px-12 py-10">
                {#if currentView === "home"}
                    <Home {vadEnabled} {transcriptHistory} {status} {uptimeString} />
                {:else if currentView === "plugins"}
                    <Plugins />
                {:else if currentView === "settings"}
                    <Settings />
                {/if}
            </div>
        </div>
        {#if showDebug}
            <div
                class="fixed bottom-4 right-4 z-[100] bg-black/80 backdrop-blur-md border border-white/10 p-3 rounded-2xl shadow-2xl flex flex-col gap-2"
            >
                <div class="flex justify-between items-center mb-1 px-1">
                    <span class="text-[9px] font-mono text-cyan-500/50 uppercase tracking-widest">Debug Console</span>
                    <button onclick={() => (showDebug = false)} class="text-white/20 hover:text-white text-xs">×</button
                    >
                </div>

                <div class="grid grid-cols-2 gap-2">
                    <button
                        onclick={() => (vadEnabled = !vadEnabled)}
                        class="px-3 py-2 rounded-lg border border-white/5 text-[10px] font-mono transition-colors
          {vadEnabled ? 'bg-cyan-500/20 text-cyan-400' : 'bg-red-500/20 text-red-400'}"
                    >
                        VAD: {vadEnabled ? "ON" : "OFF"}
                    </button>

                    <button
                        onclick={() => (status = "IDLE")}
                        class="px-3 py-2 rounded-lg border border-white/5 bg-white/5 text-[10px] font-mono hover:bg-white/10 text-slate-300"
                    >
                        SET_IDLE
                    </button>

                    <button
                        onclick={() => (status = "LISTENING")}
                        class="px-3 py-2 rounded-lg border border-white/5 bg-cyan-500/10 text-[10px] font-mono hover:bg-cyan-500/20 text-cyan-400"
                    >
                        SET_LISTEN
                    </button>

                    <button
                        onclick={() => (status = "PROCESSING")}
                        class="px-3 py-2 rounded-lg border border-white/5 bg-purple-500/10 text-[10px] font-mono hover:bg-purple-500/20 text-purple-400"
                    >
                        SET_PROC
                    </button>
                </div>

                <button
                    onclick={() => {
                        const lines = ["Hello Atlas", "What time is it?", "Sync my lights", "Play music"];
                        const randomLine = lines[Math.floor(Math.random() * lines.length)];
                        transcriptHistory = [...transcriptHistory, randomLine];
                    }}
                    class="w-full py-2 rounded-lg border border-white/5 bg-white/5 text-[10px] font-mono hover:bg-white/10 text-slate-400"
                >
                    + MOCK_TRANSCRIPT
                </button>
            </div>
        {/if}
    </main>
{/if}

<style>
    :global(html, body) {
        margin: 0;
        padding: 0;
        background-color: #0a0a0c;
        overflow: hidden;
    }
</style>
