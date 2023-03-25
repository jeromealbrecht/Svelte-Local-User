<script>
// import Form
import Form from './form.svelte';

// import UserSpace
import UserSpace from './user/userSpace.svelte';

// import browser
import {
    browser
} from "$app/environment";

// Importer le store
import {
    get
} from 'svelte/store';

// import userAll pour le store
import {
    userAll
} from '../lib/stores.js';

// import onMount onDestroy
import {
    onMount,
    onDestroy
} from 'svelte';

export const title = 'My SvelteKit App';

/**
 * @type {{ uid: string, name: string, mail: string } | null}
 */
let user = null;

let isLoading = true;

function getUserFromLocalStorage() {
    const userJson = localStorage.getItem('userAll');
    return userJson ? JSON.parse(userJson) : null;
}

/**
 * @param {{ uid: string, name: string, mail: string } | null} value
 */
function setUser(value) {
    if (browser) {
        if (value) {
            localStorage.setItem('userAll', JSON.stringify(value));
        } else {
            localStorage.removeItem('userAll');
        }
    }
    user = value;
    isLoading = false;
}

function logout() {
    setUser(null);
    userAll.set(null); // Mettre à jour le store userAll
}

/**
 * @type {import("svelte/store").Unsubscriber}
 */
let unsubscribe;

onMount(() => {
    // Vérifie si le code s'exécute dans un navigateur (et non dans un environnement SSR)
    if (browser) {
        // Récupère l'utilisateur depuis le stockage local du navigateur
        const localUser = getUserFromLocalStorage();

        // Vérifie si un utilisateur a été trouvé dans le stockage local
        if (localUser) {
            // Met à jour la variable 'user' avec les informations de l'utilisateur récupérées
            setUser(localUser);
            
            // Met à jour le store 'userAll' avec les informations de l'utilisateur récupérées
            userAll.set(localUser);
        }
        
        // Indique que le chargement est terminé, permettant d'afficher le composant approprié
        isLoading = false;
    }

    // S'abonner au store userAll
    unsubscribe = userAll.subscribe((value) => {
        setUser(value);
    });
});

onDestroy(() => {
    // Se désabonner du store userAll
    if (unsubscribe) {
        unsubscribe();
    }
});
</script>

<main>
    {#if isLoading}
    <p>Loading...</p>
    {:else if user}
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
