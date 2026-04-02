<script>
    let { title, subtitle, children, class: customClass = "" } = $props();
</script>

<div class="bg-white/[0.02] border border-white/5 rounded-2xl p-4 flex flex-col min-h-0 overflow-hidden {customClass}">
    {#if title || subtitle}
        <div class="mb-3 flex justify-between items-end border-b border-white/[0.03] pb-2 shrink-0">
            <div class="flex flex-col gap-0.5">
                <span class="text-[9px] font-bold uppercase tracking-[0.2em] text-white/30 truncate">{title}</span>
                {#if subtitle}
                    <span class="text-base font-bold text-cyan-400 uppercase italic leading-tight truncate"
                        >{subtitle}</span
                    >
                {/if}
            </div>
        </div>
    {/if}

    <div class="flex-1 relative flex flex-col min-h-0">
        {@render children?.()}
    </div>
</div>
