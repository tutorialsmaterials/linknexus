<script lang="ts">
	import { SpecialProps, type IUser, ButtonStyle, Font } from '$lib/types';
	import Gravatar from 'svelte-gravatar';
	import Analytics from './Analytics.svelte';
	export let user: IUser;
</script>
<Analytics measurement_id={user.measurement_id} />

<link rel="preconnect" href="https://fonts.googleapis.com" />
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
<link
	href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap"
	rel="stylesheet"
/>
<div
	class="container"
	style="--font-color: {user.theme.fontColor}; --bt-color: {user.theme.button
		.color}; --bt-font-color: {user.theme.button.fontColor}; --font-color: {user.theme
		.fontColor}; --bt-border: {ButtonStyle[user.theme.button.style]}; --bt-border-color: {user.theme
		.button.borderColor}; font-family: {Font[user.theme.font] === 'Times'
		? 'Times New Roman'
		: Font[user.theme.font]}, Inter;"
>
	<div class="main" style={user.theme.display == 0 ? 'max-width: 640px;' : 'max-width: 90%'}>
		<Gravatar email={user.email} size={120} default="mp" rating="pg" style="border-radius: 50%;" />

		<h1>@{user.username}</h1>
		{#if user.bio}
			<p>{user.bio}</p>
		{/if}
		{#if user.theme.display == 0}
			<ul class="linkContainer">
				{#each user.links as link}
					<li class="linkItem">
						<a href={link.url} target="_blank" rel="noopener noreferrer">
							{#if link.image}
								<img class="previewImg" src={link.image} alt={link.title} />
							{/if}
							<span style={link.image ? 'margin-right: 55px' : ''}>{link.title}</span>
						</a>
					</li>
				{/each}
			</ul>
		{:else if user.theme.display == 1}
			<div class="linkGrid">
				{#each user.links as link}
					<a href={link.url} target="_blank" class="linkGridItem">
						{#if link.image}
							<img class="gridPreviewImg" src={link.image} alt={link.title} loading="lazy" />
						{/if}
						<p style={link.image ? 'margin-bottom: 10px;' : 'margin-bottom: 0px;'}>{link.title}</p>
					</a>
				{/each}
			</div>
		{/if}
		<ul class="specialContainer">
			{#each user.specials as link}
				<li class="specialItem">
					<a
						href={SpecialProps[link.type].template(link.username)}
						target="_blank"
						rel="noopener noreferrer"
					>
						<i class="fa-brands {SpecialProps[link.type].icon}" />
					</a>
				</li>
			{/each}
		</ul>
	</div>
</div>

<style>
	div.container {
		background: transparent;
		color: var(--font-color, #333);
	}

	div.main {
		text-align: center;
		margin: 0 auto;
	}

	ul {
		list-style: none;
		padding: 0;
		margin: 0;
	}

	li.linkItem {
		background-color: var(--bt-color, #fafafa);
		border: 2px var(--bt-border) var(--bt-border-color, #e8e8ed);
		border-radius: 0.5rem;
		margin: 0.5rem 0;
		display: flex;
		align-items: center;
		min-height: 3.75rem;
		text-align: center;
	}

	li.linkItem a {
		display: flex;
		align-items: center;
		padding: 0.4rem;
		text-decoration: none;
		height: 100%;
		text-align: center;
		flex: 1;
	}

	li.linkItem a span {
		color: var(--bt-font-color, #333);
		display: block;
		width: 100%;
	}

	.previewImg {
		border-radius: 4px;
		margin-right: 10px;
		width: 48px;
		height: 48px;
	}

	li.specialItem a {
		font-size: 2.38rem;
		color: black;
		margin: 0 0.5rem;
		color: var(--font-color);
	}

	.linkContainer {
		margin-top: 2rem;
	}

	.specialContainer {
		display: flex;
		align-items: center;
		justify-content: center;
		margin-top: 2rem;
		overflow: hidden;
		flex-wrap: wrap;
		line-height: 3rem;
	}

	h1 {
		margin-top: 1rem;
		font-size: 1.25rem;
	}

	p {
		margin-top: 0.5rem;
		font-size: 1rem;
	}

	/* when a link hovers, enlarge it via an animation */
	li.linkItem:hover,
	li.specialItem:hover {
		animation: enlarge 0.2s ease-in-out forwards;
	}

	.linkGrid {
		display: grid;
		grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
		grid-gap: 10px;
		justify-items: center;
		align-items: center;
		margin-top: 2rem;
	}

	.linkGridItem {
		width: 100%;
		height: 200px;
		border-radius: 0.5rem;
		border: 2px var(--bt-border) var(--bt-border-color, #e8e8ed);
		background-color: var(--bt-color, #fafafa);
		border: 2px var(--bt-border) var(--bt-border-color, #e8e8ed);
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		color: var(--bt-font-color, #333);
		text-decoration: none;
	}

	.linkGridItem:hover {
		animation: enlarge 0.2s ease-in-out forwards;
		cursor: pointer;
	}

	/* image at top and p at bottom */
	.linkGridItem p {
		font-size: 1rem;
		margin-top: 0;
	}

	.gridPreviewImg {
		max-height: 140px;
		max-width: 140px;
		margin: auto 0;
		border-radius: 0.5rem;
		object-fit: cover;
	}

	@keyframes enlarge {
		0% {
			transform: scale(1);
		}

		100% {
			transform: scale(1.03);
		}
	}
</style>
