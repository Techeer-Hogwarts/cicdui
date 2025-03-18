<script lang="ts">
	import { page } from '$app/state';
</script>

<header>
	<nav>
		<!-- make the component to be centered -->
		<ul>
			<li aria-current={page.url.pathname === '/login' ? 'page' : undefined}>
				<a href="/login">Login</a>
			</li>
			<li aria-current={page.url.pathname === '/reset' ? 'page' : undefined}>
				<a href="/reset">Reset</a>
			</li>
			<li aria-current={page.url.pathname === '/workflows' ? 'page' : undefined}>
				<a href="/workflows">Workflows</a>
			</li>
		</ul>
	</nav>
</header>

<style>
	header {
		display: flex;
		justify-content: center;
	}
	nav {
		display: flex;
		justify-content: center;
	}

	ul {
		position: relative;
		padding: 0;
		margin: 0;
		height: 3em;
		display: flex;
		justify-content: center;
		align-items: center;
		list-style: none;
		background: var(--background);
		background-size: contain;
	}

	li {
		position: relative;
		height: 100%;
	}

	li[aria-current='page']::before {
		--size: 6px;
		content: '';
		width: 0;
		height: 0;
		position: absolute;
		top: 0;
		left: calc(50% - var(--size));
		border: var(--size) solid transparent;
		border-top: var(--size) solid var(--color-theme-1);
	}

	nav a {
		display: flex;
		height: 100%;
		align-items: center;
		padding: 0 0.5rem;
		color: var(--color-text);
		font-weight: 700;
		font-size: 0.8rem;
		text-transform: uppercase;
		letter-spacing: 0.1em;
		text-decoration: none;
		transition: color 0.2s linear;
	}

	a:hover {
		color: var(--color-theme-1);
	}
</style>
