<script lang="ts">
	import { goto } from '$app/navigation';

	let email = '';
	let password = '';
	let rememberMe = false;
	let loading = false;
	let errorMessage = '';

	// Client-side email validation
	function isValidEmail(email: string): boolean {
		return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
	}

	async function handleSubmit(event: SubmitEvent) {
		event.preventDefault();
		errorMessage = '';

		if (!email || !password) {
			errorMessage = 'Please fill in all fields';
			return;
		}

		if (!isValidEmail(email)) {
			errorMessage = 'Please enter a valid email address';
			return;
		}

		try {
			loading = true;

			const response = await fetch('https://your-api-server.com/login', {
				method: 'POST',
				headers: {
					'Content-Type': 'application/json'
				},
				body: JSON.stringify({ email, password, rememberMe })
			});

			const data = await response.json();

			if (!response.ok) {
				throw new Error(data.message || 'Login failed');
			}

			if (data.requires_password_reset) {
				goto('/reset-password'); // Redirect user to password reset page
				return;
			}

			// Store the token
			if (data.token) {
				localStorage.setItem('token', data.token);
			}

			// Redirect to dashboard if no reset is needed
			goto('/dashboard');
		} catch (error) {
			errorMessage = error instanceof Error ? error.message : 'An error occurred during login';
		} finally {
			loading = false;
		}
	}
</script>

<svelte:head>
	<title>Login</title>
</svelte:head>

<div class="flex min-h-screen items-center justify-center bg-gray-50">
	<div class="w-full max-w-md space-y-8 rounded-lg bg-white p-8 shadow">
		<div class="text-center">
			<h1 class="text-3xl font-extrabold text-gray-900">Sign In</h1>
		</div>

		<form class="mt-8 space-y-6" on:submit={handleSubmit}>
			{#if errorMessage}
				<div class="rounded-md bg-red-50 p-4 text-red-800">
					{errorMessage}
				</div>
			{/if}

			<div class="-space-y-px rounded-md">
				<div>
					<label for="email" class="sr-only">Email</label>
					<input
						id="email"
						bind:value={email}
						type="email"
						autocomplete="email"
						required
						class="relative block w-full appearance-none rounded-none rounded-t-md border border-gray-300 px-3 py-2 text-gray-900 placeholder-gray-500 focus:z-10 focus:border-indigo-500 focus:ring-indigo-500 focus:outline-none sm:text-sm"
						placeholder="Email address"
					/>
				</div>
				<div>
					<label for="password" class="sr-only">Password</label>
					<input
						id="password"
						bind:value={password}
						type="password"
						autocomplete="current-password"
						required
						class="relative block w-full appearance-none rounded-none rounded-b-md border border-gray-300 px-3 py-2 text-gray-900 placeholder-gray-500 focus:z-10 focus:border-indigo-500 focus:ring-indigo-500 focus:outline-none sm:text-sm"
						placeholder="Password"
					/>
				</div>
			</div>

			<!-- <div class="flex items-center justify-between">
          <div class="flex items-center"> -->
			<!-- <input
              id="remember-me"
              bind:checked={rememberMe}
              type="checkbox"
              class="h-4 w-4 text-indigo-600 focus:ring-indigo-500 border-gray-300 rounded"
            /> -->
			<!-- <label for="remember-me" class="ml-2 block text-sm text-gray-900">
              Remember me
            </label> -->
			<!-- </div>
        </div> -->

			<div>
				<button
					type="submit"
					class="group relative flex w-full justify-center rounded-md border border-transparent bg-indigo-600 px-4 py-2 text-sm font-medium text-white hover:bg-indigo-700 focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2 focus:outline-none"
					disabled={loading}
				>
					{#if loading}
						<span class="absolute inset-y-0 left-0 flex items-center pl-3">
							<svg
								class="h-5 w-5 animate-spin text-indigo-300"
								xmlns="http://www.w3.org/2000/svg"
								fill="none"
								viewBox="0 0 24 24"
							>
								<circle
									class="opacity-25"
									cx="12"
									cy="12"
									r="10"
									stroke="currentColor"
									stroke-width="4"
								></circle>
								<path
									class="opacity-75"
									fill="currentColor"
									d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
								></path>
							</svg>
						</span>
						Signing in...
					{:else}
						Sign in
					{/if}
				</button>
			</div>
		</form>
	</div>
</div>
