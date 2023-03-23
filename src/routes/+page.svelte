<script>
import Form from './form.svelte';
import UserSpace from './user/userSpace.svelte';
import {
    browser
} from "$app/environment";

// Importer le store
import {
    get
} from 'svelte/store'
// import userAll pour le store
import {
    userAll
} from '../lib/stores.js';
// import onMount
import {
    onMount
} from 'svelte';

export const title = 'My SvelteKit App';

/**
 * @type {string | null}
 */
let user;

// let name = 'name';
let isLoading = true;

/**
 * @param {any} value
 */
function setUser(value) {
    // stocker l'utilisateur dans le stockage local
    localStorage.setItem('userAll', value);
    user = value;
    isLoading = false;
}

let unsubscribe;

onMount(() => {
    // récupérer l'utilisateur stocké dans le store svelte
    unsubscribe = userAll.subscribe(
        (value) => {
            setUser(value);
        }
    );

    // récupérer l'utilisateur stocké dans le stockage local
        console.log("local user", user);

        // Vérifier si la valeur de "user" est déjà stockée dans localStorage
        if (!localStorage.getItem('userAll') && !user) {
            user = localStorage.getItem('userAll');
        }
});

function logout() {
    setUser(null);
    if (browser) {
        localStorage.removeItem('userAll');
    }
}

</script>

<main>
    {#if user}
    <UserSpace name={user.name} />
    <button on:click={logout}>Logout</button>
    {:else}
    <Form />
    {/if}
</main>

<style>
@import url('https://fonts.googleapis.com/css2?family=Oswald:wght@300;400;500;600;700&display=swap');

:global(body) {
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    margin: 0;
    padding: 0;
}

main {
    width: 100%;
    height: 100vh;
    display: flex;
    justify-content: center;
    font-family: "Oswald";
    position: relative;
}
</style>
