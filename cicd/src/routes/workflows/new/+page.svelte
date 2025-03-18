<script lang="ts">
	import { onMount } from 'svelte';
	import { writable } from 'svelte/store';

	// Define writable stores for form data
	let name = writable('');
	let type = writable('ci'); // Default to 'ci'
	let source = writable('');
	let imageName = writable('');
	let githubToken = writable('');
	let workflows = writable([]);
	let selectedWorkflow = writable('');

	// State to show/hide workflow selection
	let loadingWorkflows = writable(false);
	// let showWorkflowDropdown = writable(false);

	// Fetch workflows on component mount (onMount)
	onMount(() => {
		// Logic that should run when the component is first rendered
		console.log('Component mounted!');
	});

	// Function to fetch available workflows from GitHub
	async function fetchWorkflows() {
		const sourceUrl = $source;
		if (!sourceUrl.includes('github.com')) return;

		$loadingWorkflows = true;
		const urlParts = sourceUrl.split('/');
		const owner = urlParts[3];
		const repo = urlParts[4];

		try {
			const response = await fetch(
				`https://api.github.com/repos/${owner}/${repo}/actions/workflows`,
				{
					headers: {
						Authorization: `token ${$githubToken}`
					}
				}
			);

			const data = await response.json();
			$workflows = data.workflows || [];
			if ($workflows.length === 1) $selectedWorkflow = $workflows[0].name;
		} catch (error) {
			console.error('Failed to fetch workflows:', error);
		} finally {
			$loadingWorkflows = false;
		}
	}

	// Create workflow function
	async function createWorkflow() {
		const payload = {
			name: $name,
			type: $type,
			source: $source,
			imageName: $imageName,
			githubToken: $githubToken,
			workflowName: $selectedWorkflow
		};

		try {
			const response = await fetch('/api/workflows', {
				method: 'POST',
				headers: { 'Content-Type': 'application/json' },
				body: JSON.stringify(payload)
			});

			if (response.ok) {
				alert('Workflow created successfully!');
				window.location.href = '/workflows';
			} else {
				alert('Failed to create workflow.');
			}
		} catch (error) {
			console.error('Error creating workflow:', error);
		}
	}
</script>

<div class="mx-auto max-w-lg rounded-lg bg-white p-6 shadow-md">
	<h2 class="mb-4 text-2xl font-semibold">Create Workflow</h2>

	<div class="mb-4">
		<label class="block text-sm font-medium">Name</label>
		<input bind:value={$name} class="w-full rounded-md border p-2" placeholder="Workflow name" />
	</div>

	<div class="mb-4">
		<label class="block text-sm font-medium">Type</label>
		<select bind:value={$type} class="w-full rounded-md border p-2">
			<option value="ci">CI</option>
			<option value="cd">CD</option>
		</select>
	</div>

	<div class="mb-4">
		<label class="block text-sm font-medium">GitHub Repository URL</label>
		<input
			bind:value={$source}
			class="w-full rounded-md border p-2"
			placeholder="https://github.com/org/repo"
		/>
	</div>

	<div class="mb-4">
		<label class="block text-sm font-medium">Image Name</label>
		<input
			bind:value={$imageName}
			class="w-full rounded-md border p-2"
			placeholder="Docker image name"
		/>
	</div>
	{#if $type === 'cd'}
		<div class="mb-4">
			<label class="block text-sm font-medium">GitHub Token</label>
			<input
				bind:value={$githubToken}
				type="password"
				class="w-full rounded-md border p-2"
				on:blur={fetchWorkflows}
				placeholder="GitHub Auth Token"
			/>
		</div>
	{/if}

	{#if $loadingWorkflows}
		<p class="text-sm text-gray-500">Fetching available workflows...</p>
	{/if}

	{#if $type === 'cd'}
		<div class="mb-4">
			<label class="block text-sm font-medium">Select Workflow</label>
			<select bind:value={$selectedWorkflow} class="w-full rounded-md border p-2">
				{#each $workflows as workflow}
					<option value={workflow.id}>{workflow.name} - {workflow.path}</option>
				{/each}
			</select>
		</div>
	{/if}

	<button on:click={createWorkflow} class="w-full rounded-md bg-blue-600 py-2 text-white">
		워크플로우 수정
	</button>
</div>
