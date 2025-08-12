<script>
    let { showModal = $bindable(), header, children } = $props();

    let dialog = $state();

    $effect(() => {
        if (showModal) dialog.showModal();
    });
</script>

<dialog>
    yar
</dialog>