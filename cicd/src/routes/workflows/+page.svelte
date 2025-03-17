<script>
	import { onMount } from 'svelte';
	import { fade } from 'svelte/transition';
	import WorkflowCard from './WorkflowCard.svelte';

	let workflows = [];
	let isLoading = true;
	let error = null;

	onMount(async () => {
		try {
			// const response = await fetch('YOUR_API_ENDPOINT_HERE');
			// if (!response.ok) {
			//   throw new Error(`Error fetching workflows: ${response.statusText}`);
			// }
			// workflows = await response.json();
			workflows = [
				{
					id: '1',
					name: 'Image Processing',
					type: 'CI',
					source: 'GitHub',
					imageName: 'image-processing:v1',
					lastEvent: '2023-08-01T12:00:00Z'
				},
				{
					id: '2',
					name: 'Data Analysis',
					type: 'CI',
					source: 'GitLab',
					imageName: 'data-analysis:v2',
					lastEvent: '2023-08-01T12:00:00Z'
				},
				{
					id: '3',
					name: 'Machine Learning Training',
					type: 'CD',
					source: 'Bitbucket',
					imageName: 'ml-training:v3',
					lastEvent: '2023-08-01T12:00:00Z'
				}
			];
		} catch (err) {
			error = err.message;
		} finally {
			isLoading = false;
		}
	});

	function handleRemoveWorkflow(id) {
		workflows = workflows.filter((workflow) => workflow.id !== id);
	}
</script>

<div class="container py-10">
	<div class="mb-8 flex items-center justify-between">
		<div>
			<h1 class="text-3xl font-bold tracking-tight">Workflows</h1>
			<!-- <p class="text-muted-foreground mt-1">Manage your pipeline workflows and deployments</p> -->
		</div>
		<div>
			<a
				href="/workflows/new"
				class="ring-offset-background focus-visible:ring-ring bg-primary text-primary-foreground hover:bg-primary/90 inline-flex h-10 items-center justify-center rounded-md px-4 py-2 text-sm font-medium transition-colors focus-visible:ring-2 focus-visible:ring-offset-2 focus-visible:outline-none disabled:pointer-events-none disabled:opacity-50"
			>
				새로운 Workflow 만들기
			</a>
		</div>
	</div>

	{#if isLoading}
		<div class="flex items-center justify-center py-12">
			<div class="border-primary h-8 w-8 animate-spin rounded-full border-b-2"></div>
		</div>
	{:else if error}
		<div class="bg-destructive/10 text-destructive rounded-md p-4">
			<p>Failed to load workflows: {error}</p>
			<button class="mt-2 text-sm underline" on:click={() => window.location.reload()}>
				Try again
			</button>
		</div>
	{:else if workflows.length === 0}
		<div
			in:fade
			class="flex flex-col items-center justify-center rounded-lg border border-dashed p-8 text-center"
		>
			<div class="bg-primary/10 mx-auto flex h-12 w-12 items-center justify-center rounded-full">
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
					class="text-primary h-6 w-6"
				>
					<path
						d="M14.7 6.3a1 1 0 0 0 0 1.4l1.6 1.6a1 1 0 0 0 1.4 0l3.77-3.77a6 6 0 0 1-7.94 7.94l-6.91 6.91a2.12 2.12 0 0 1-3-3l6.91-6.91a6 6 0 0 1 7.94-7.94l-3.76 3.76z"
					/>
				</svg>
			</div>
			<h3 class="mt-4 text-lg font-semibold">No workflows found</h3>
			<p class="text-muted-foreground mt-2 text-sm">
				You haven't created any workflows yet. Get started by creating one.
			</p>
			<a
				href="/workflows/new"
				class="ring-offset-background focus-visible:ring-ring bg-primary text-primary-foreground hover:bg-primary/90 mt-4 inline-flex h-10 items-center justify-center rounded-md px-4 py-2 text-sm font-medium transition-colors focus-visible:ring-2 focus-visible:ring-offset-2 focus-visible:outline-none disabled:pointer-events-none disabled:opacity-50"
			>
				Create Workflow
			</a>
		</div>
	{:else}
		<div class="space-y-4">
			{#each workflows as workflow (workflow.id)}
				<div in:fade={{ duration: 200 }}>
					<WorkflowCard {workflow} onRemove={handleRemoveWorkflow} />
				</div>
			{/each}
		</div>
	{/if}
</div>
