<script context="module" lang="ts">
	export type Workflow = {
		id: string;
		name: string;
		type: string;
		source: string;
		imageName: string;
		lastEvent: string;
	};
	import { fade } from 'svelte/transition';
</script>

<script lang="ts">
	export let workflow: Workflow;
	export let onRemove: (id: string) => void;

	let showDeleteDialog = false;
	let dropdownOpen = false;

	let dropdownRef: HTMLDivElement | null = null;

	function showDetailedPage(id: string) {
		window.location.href = `/workflows/${id}`;
	}

	function handleEdit() {
		window.location.href = `/workflows/edit/${workflow.id}`;
	}

	function handleRemove() {
		console.log('Removing workflow:', workflow.id);
		showDeleteDialog = true;
		dropdownOpen = false;
	}

	function confirmRemove() {
		onRemove(workflow.id);
		showDeleteDialog = false;
	}

	function cancelRemove() {
		showDeleteDialog = false;
	}

	function handleClickOutside(event: MouseEvent) {
		if (
			dropdownOpen &&
			!(event.target as Element)?.closest('.dropdown-content') &&
			!(event.target as Element)?.closest('.dropdown-trigger')
		) {
			dropdownOpen = false;
		}
	}
</script>

<div
	class="bg-card text-card-foreground rounded-lg border transition-shadow hover:shadow-md"
	on:click={() => showDetailedPage(workflow.id)}
	on:keydown={(e) => e.key === 'Enter' && showDetailedPage(workflow.id)}
	role="button"
	tabindex="0"
>
	<div class="p-6">
		<div class="flex items-center justify-between">
			<div class="grid gap-1">
				<h3 class="text-lg font-semibold">{workflow.name}</h3>
				<div class="mt-2 grid grid-cols-1 gap-x-4 gap-y-2 md:grid-cols-4">
					<div>
						<p class="text-muted-foreground text-sm font-medium">Type</p>
						<p class="font-mono text-sm">{workflow.type}</p>
					</div>
					<div>
						<p class="text-muted-foreground text-sm font-medium">Source</p>
						<p class="font-mono text-sm">{workflow.source}</p>
					</div>
					<div>
						<p class="text-muted-foreground text-sm font-medium">Image Name</p>
						<p class="font-mono text-sm">{workflow.imageName}</p>
					</div>
					<div>
						<p class="text-muted-foreground text-sm font-medium">Last Event</p>
						<p class="font-mono text-sm">{workflow.lastEvent}</p>
					</div>
				</div>
			</div>

			<div class="relative">
				<button
					class="hover:bg-muted dropdown-trigger flex h-8 w-8 items-center justify-center rounded-md border transition-colors"
					on:click|stopPropagation={() => (dropdownOpen = !dropdownOpen)}
					aria-haspopup="true"
					aria-expanded={dropdownOpen}
				>
					<svg
						xmlns="http://www.w3.org/2000/svg"
						width="24"
						height="24"
						viewBox="0 0 24 24"
						fill="none"
						stroke="currentColor"
						stroke-width="2"
						stroke-linecap="round"
						stroke-linejoin="round"
						class="h-4 w-4"
					>
						<circle cx="12" cy="12" r="1" />
						<circle cx="12" cy="5" r="1" />
						<circle cx="12" cy="19" r="1" />
					</svg>
					<span class="sr-only">Open menu</span>
				</button>

				{#if dropdownOpen}
					<div
						class="text-popover-foreground animate-in fade-in-80 dropdown-content absolute right-0 z-10 mt-2 w-36 origin-top-right rounded-md bg-white p-1 shadow-md"
						transition:fade={{ duration: 100 }}
						bind:this={dropdownRef}
					>
						<button
							class="hover:bg-accent hover:text-accent-foreground relative flex w-full cursor-default items-center rounded-sm px-2 py-1.5 text-sm outline-none select-none"
							on:click|stopPropagation={handleEdit}
						>
							<svg
								xmlns="http://www.w3.org/2000/svg"
								width="24"
								height="24"
								viewBox="0 0 24 24"
								fill="none"
								stroke="currentColor"
								stroke-width="2"
								stroke-linecap="round"
								stroke-linejoin="round"
								class="mr-2 h-4 w-4"
							>
								<path d="M17 3a2.85 2.83 0 1 1 4 4L7.5 20.5 2 22l1.5-5.5Z" />
							</svg>
							수정
						</button>
						<button
							class="text-destructive hover:bg-destructive hover:text-destructive-foreground relative flex w-full cursor-default items-center rounded-sm px-2 py-1.5 text-sm outline-none select-none"
							on:click|stopPropagation={handleRemove}
						>
							<svg
								xmlns="http://www.w3.org/2000/svg"
								width="24"
								height="24"
								viewBox="0 0 24 24"
								fill="none"
								stroke="currentColor"
								stroke-width="2"
								stroke-linecap="round"
								stroke-linejoin="round"
								class="mr-2 h-4 w-4"
							>
								<path d="M3 6h18" />
								<path d="M19 6v14c0 1-1 2-2 2H7c-1 0-2-1-2-2V6" />
								<path d="M10 11v6" />
								<path d="M14 11v6" />
							</svg>
							삭제
						</button>
					</div>
				{/if}
			</div>
		</div>
	</div>
</div>
{#if showDeleteDialog}
	<div class="bg-opacity-10 fixed inset-0 z-50 flex items-center justify-center backdrop-blur-xs">
		<div class="w-96 rounded-lg bg-white p-6 shadow-lg">
			<h2 class="mb-4 text-xl font-semibold">Are you sure you want to delete this workflow?</h2>
			<div class="flex justify-between">
				<button class="rounded-md bg-gray-500 px-4 py-2 text-white" on:click={cancelRemove}>
					Cancel
				</button>
				<button class="rounded-md bg-red-500 px-4 py-2 text-white" on:click={confirmRemove}>
					Confirm Remove
				</button>
			</div>
		</div>
	</div>
{/if}
