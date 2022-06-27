<script lang="ts">
	import Header from "./components/Header.svelte";
	import Content from "./components/Content.svelte";
	import Footer from "./components/Footer.svelte";
	import Loader from "./components/Loader.svelte";
</script>



<Header />
<Content>
	<Loader />
</Content>
<Footer />



<style lang="scss">
	@import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@100;300;400;500;700;900&display=swap');

	:global(html) {
		height: 100%;
	}

	:global(body) {
		display: flex;
		flex-direction: column;
		min-height: 100%;
		margin: 0;

		font-family: "Noto Sans KR";
	}
</style>