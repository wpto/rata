<script>
    import closeIcon from './assets/x.svg'
    export let label
    let disabled
    let type
    let placeholder
    export let value = ""
    let onFocus
    let hidden
    let clear
    let id
    let error
    let errorMsgHidden
</script>

<div class="h-full flex flex-col justify-end">
    <label class="block grow-0 shrink-0 basis-auto mb-1" for={id}>{label}</label>
    <div class="relative">
        <input
            class="block w-full pr-2 text-xl border border-solid border-amber-600 rounded-sm bg-white
            disabled:bg-clip-padding disabled:text-black disabled:bg-transparent disabled:border-white disabled:opacity-100 disabled:rounded-none disabled:border-b-2
            {disabled && "border-orange-700 disabled:border-b-orange-700"}"
            {type}
            {placeholder}
            {disabled}
            bind:value={value}
            on:focus={onFocus}
            id={id}
        />
        <button
            class="absolute right-8px top-10px bottom-10px mb-0 p-0 {hidden || disabled ? 'hidden': ''}"
            type="button"
            on:click={clear}
        >
            <img class="opacity-70" src={closeIcon} alt="clear" />
        </button>
    </div>
    <label class={errorMsgHidden ? 'opacity-0' : ''} for={id}>{error}</label>
</div>