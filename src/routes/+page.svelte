<script lang="ts">
    import { autofocus } from "$lib/autofocus"
    import { writable } from 'svelte-local-storage-store';

    let editingIndex = -1;

	const messages = writable<string[]>("todo-list", []);
	let newMessage = '';
</script>

<ul>
	{#each $messages as message, i}
        {#if editingIndex == i}
            <input use:autofocus bind:value={newMessage} on:keydown={(event) => {
                if (event.key === "Enter" || event.key === "ArrowDown") {
                    $messages[editingIndex] = newMessage;
                    if (editingIndex === $messages.length - 1) {
                        editingIndex = -1;
                        newMessage = '';
                        return;
                    }
                    editingIndex++;
                    newMessage = $messages[editingIndex];
                } else if (event.key === "ArrowUp") {
                    if (editingIndex === 0) return;
                    $messages[editingIndex] = newMessage;
                    editingIndex--;
                    newMessage = $messages[editingIndex];
                } else if (event.key === "Backspace" && newMessage.length === 0) {
                    if (editingIndex === 0 && $messages.length === 1) return;
                    if (editingIndex === $messages.length - 1) editingIndex = $messages.length - 2;
                    newMessage = $messages[editingIndex]
                    $messages = [...$messages.slice(0, editingIndex), ...$messages.slice(editingIndex + 1)];
                    event.preventDefault();
                }
            }}>
        {:else}
		    <li>{message}</li>
        {/if}
	{/each}
    {#if editingIndex === -1}
        <input
            use:autofocus
            bind:value={newMessage}
            placeholder="Enter message"
            on:keydown={(event) => {
                if (event.key === 'Enter') {
                    if (newMessage.length === 0) return
                    $messages = [...$messages, newMessage];
                    newMessage = '';
                }

                if (event.key === "ArrowUp" || (event.key === "Backspace" && newMessage.length === 0)) {
                    if (newMessage) $messages = [...$messages, newMessage];
                    editingIndex = $messages.length - 1;
                    newMessage = $messages[editingIndex];
                    event.preventDefault();
                }
            }}
        />
    {/if}
</ul>
