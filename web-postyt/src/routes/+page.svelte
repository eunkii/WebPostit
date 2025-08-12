<script lang="ts">
    import { draggable, droppable, type DragDropState } from '@thisux/sveltednd';
    import { flip } from 'svelte/animate';
    import Modal from './CardModal.svelte'

    interface Card {
        id: string;
        content: string;
    }

    let cards = $state<Card[]>([]);

    function handleAdd() {
        cards = [ ...cards, {id: String(cards.length+1), content: ''} ];
    }

    function handleRemove(id: string) {
        cards = cards.filter(card => card.id !== id);
    }

    /* if selected container is not the same as container to drop to,
        handleDrop extracts selected item from cards array, and reorder */
    function handleDrop(state: DragDropState<Card>) {
        const { draggedItem, sourceContainer, targetContainer } = state;

        if (!targetContainer || sourceContainer === targetContainer) return;

        cards = cards.filter( (card: Card) => card.id !== draggedItem.id );
        cards.splice(parseInt(targetContainer), 0, draggedItem);
    }
</script>

<div class="min-h-screen p-8">
    <div class="grid grid-cols-7 gap-6">
        {#each cards as card, index(card.id)}
            <div
                use:droppable={ {container: index.toString(), callbacks: { onDrop: handleDrop }} } 
                class="relative aspect-square rounded-md bg-white shadow-md transition-all duration-300 hover:scale-105 hover:shadow-xl"
                animate:flip={ {duration:200} }
            >
                <button
                    use:draggable={ {container: index.toString(), dragData: card} } 
                    class="h-full w-full cursor-grab transition-all duration-300 hover:scale-105" 
                    onclick={() => (showModal = true)}
                >
                    <div class="flex h-full items-center justify-center">
                        <p class="text-4xl">{card.content}</p>
                    </div>
                </button>
                <button
                    class="flex absolute -top-2 -right-2 w-6 h-6 items-center justify-center rounded-full bg-red-500 text-white m-1 transition-all duration-300 hover:scale-120 hover:shadow-xl"
                    onclick={() => handleRemove(card.id)}
                >
                    x
                </button>
            </div>
        {/each}
        <button
            class="relative aspect-square rounded-md shadow-md transition-all duration-300 hover:scale-105 hover:shadow-xl cursor-pointer"
            onclick={handleAdd}
        >
            <div class="flex h-full items-center justify-center">
                <p class="text-4xl">+</p>
            </div>
        </button>
    </div>
</div>

<style lang="postcss">
    @reference "tailwindcss";

    :global(html) {
        background-color: theme(--color-gray-100);
    }
</style>