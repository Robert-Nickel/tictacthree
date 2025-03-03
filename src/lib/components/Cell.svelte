<script lang="ts">
    import { CellState } from "$lib/types";

    export let state: CellState = CellState.Empty;
</script>

<div class="cell" data-state={state} on:click tabindex="-1">
    {#if state === CellState.Cross}
        ✖
    {:else if state === CellState.Circle}
        ⭘
    {/if}
</div>

<style>
    .cell {
        width: 100px;
        height: 100px;
        display: flex;
        align-items: center;
        justify-content: center;
        font-size: 2rem;
        border: 2px solid #000;
        cursor: pointer;
        user-select: none;
        outline: none;
        -webkit-tap-highlight-color: transparent; /* Remove blue highlight on mobile */
        user-select: none;
    }

    .cell:focus,
    .cell:active {
        outline: none;
        background: inherit;
        box-shadow: none;
    }
</style>
