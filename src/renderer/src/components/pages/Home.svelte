<script>
    import { fade, scale, fly } from "svelte/transition";
    import Module from "../Module.svelte";

    let { status = "IDLE", transcriptHistory = [], vadEnabled = true, uptimeString = "" } = $props();

    // Mock data for the sidebar
    let activePlugins = ["Spotify", "Home-Assistant", "Web-Search"];
</script>

<div in:fade class="h-full flex flex-col gap-4 overflow-hidden">
    <div class="grid grid-cols-3 gap-4 h-24 shrink-0">
        <Module title="Engine" subtitle={vadEnabled ? status : "OFFLINE"} />
        <Module title="Plugins" subtitle="{activePlugins.length} Modules" />
        <Module title="Uptime" subtitle={uptimeString} />
    </div>

    <div class="flex-1 grid grid-cols-12 gap-4 min-h-0">
        <Module title="Frequency Monitor" class="col-span-8 bg-white/[0.005]! min-h-0">
            <div class="flex flex-col items-center justify-center h-full relative">
                <div class="absolute w-32 h-32 border border-white/[0.03] rounded-full z-0"></div>

                <div class="relative w-full h-40 flex items-center justify-center">
                    {#if !vadEnabled}
                        <div in:scale out:fade class="absolute flex flex-col items-center gap-3">
                            <div
                                class="w-12 h-12 rounded-full border-2 border-dashed border-white/10 flex items-center justify-center"
                            >
                                <div class="w-2 h-2 bg-white/5 rounded-full"></div>
                            </div>
                        </div>
                    {:else if status === "LISTENING"}
                        <div in:scale out:fade class="absolute flex items-center justify-center gap-1.5 h-16">
                            {#each Array(12) as _, i (i)}
                                <div
                                    class="w-1.5 bg-cyan-400 rounded-full animate-[bounce_0.6s_infinite] shadow-[0_0_10px_rgba(34,211,238,0.5)]"
                                    style="animation-delay: {i * 0.05}s; height: {20 + Math.random() * 40}px"
                                ></div>
                            {/each}
                        </div>
                    {:else if status === "PROCESSING"}
                        <div in:scale out:fade class="absolute flex items-center justify-center">
                            <div
                                class="w-20 h-20 border-t-2 border-r-2 border-purple-500 rounded-full animate-spin shadow-[0_0_20px_rgba(168,85,247,0.2)]"
                            ></div>
                            <div class="absolute w-3 h-3 bg-purple-400 rounded-full blur-[2px] animate-pulse"></div>
                        </div>
                    {:else}
                        <div in:fade out:fade class="absolute flex items-center justify-center">
                            <div class="absolute w-16 h-16 bg-cyan-500/10 rounded-full animate-pulse blur-xl"></div>
                            <div
                                class="w-14 h-14 rounded-full border border-white/10 flex items-center justify-center bg-black/40 backdrop-blur-xl"
                            >
                                <div class="w-2 h-2 bg-cyan-400 shadow-[0_0_8px_#22d3ee] rounded-full"></div>
                            </div>
                        </div>
                    {/if}
                </div>

                <div class="absolute bottom-6 w-full text-center">
                    <p class="text-[9px] font-mono text-white/10 uppercase tracking-[0.6em]">
                        {#if !vadEnabled}
                            Locked
                        {:else if status === "LISTENING"}
                            Active_Input
                        {:else if status === "PROCESSING"}
                            Decoding_Signal
                        {:else}
                            Standby
                        {/if}
                    </p>
                </div>
            </div>
        </Module>

        <Module title="Plugins" class="col-span-4 min-h-0">
            <div class="overflow-y-auto pr-2 custom-scrollbar h-full">
                <div class="space-y-1">
                    {#each activePlugins as plugin (plugin)}
                        <div
                            class="flex items-center justify-between p-2 rounded-lg bg-white/[0.02] border border-white/5"
                        >
                            <span class="text-[10px] font-medium text-slate-400">{plugin}</span>
                            <div class="w-1 h-1 rounded-full bg-cyan-500/50"></div>
                        </div>
                    {/each}
                </div>
            </div>
        </Module>
    </div>

    <Module title="Signal History" class="h-44 shrink-0 bg-cyan-500/[0.01]">
        <div class="flex flex-col-reverse gap-2 h-full overflow-y-auto pr-4 custom-scrollbar">
            {#each transcriptHistory.slice().reverse() as line, i (line + (transcriptHistory.length - i))}
                <div
                    in:fly={{ x: -10 }}
                    class="flex items-start gap-3 {i === 0
                        ? 'opacity-100'
                        : 'opacity-30'} transition-opacity duration-500"
                >
                    <span class="text-[9px] font-mono text-cyan-500/40 mt-1">[{transcriptHistory.length - i}]</span>
                    <p class="text-xs font-light {i === 0 ? 'text-white' : 'text-slate-400'} italic line-clamp-1">
                        "{line}"
                    </p>
                </div>
            {/each}
        </div>
    </Module>
</div>

<style>
    /* Custom bounce for audio bars to feel "snappier" */
    @keyframes bounce {
        0%,
        100% {
            transform: scaleY(0.5);
        }
        50% {
            transform: scaleY(1.2);
        }
    }
</style>
