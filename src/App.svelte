<script>
  import { onMount } from "svelte";
  import classNames from "classnames";

  document.body.style.backgroundColor = "#414141";
  let appName = "Todo-list";
  let data = [];
  let name = "";
  let body = "";
  let idMax = null;
  const apiURL = "https://jsonplaceholder.typicode.com/posts/1/comments";

  onMount(async function() {
    const response = await fetch(apiURL);
    data = await response.json();
    data.forEach(function(d) {
      d.favorite = false;
    });
  });

  function addCard() {
    const idMax = Math.max(...data.map(o => o.id), 0);
    let newData = {
      postId: 1,
      id: idMax + 1,
      name: name,
      email: "",
      body: body
    };
    data = [newData, ...data];
    name = "";
    body = "";
  }

  function deleteCard(id) {
    data = data.filter(t => t.id !== Number(id));
  }

  function toggleFavorite(id) {
    const index = data.findIndex(item => item.id === Number(id));
    data[index].favorite = !data[index].favorite;
  }
</script>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  h1 {
    color: #fdf1b8;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }

  #card {
    border: 1px solid grey;
    margin-bottom: 20px;
    box-sizing: border-box;
    position: relative;
    padding: 20px;
  }

  .delete-button {
    font-weight: bold;
    color: red;
    position: absolute;
    top: 10px;
    right: 10px;
    cursor: pointer;
  }

  .favorite-button {
    font-weight: bold;
    color: #ffd700;
    position: absolute;
    top: 10px;
    right: 25px;
    cursor: pointer;
  }

  .favorite-card {
    border: 1px solid #ffd700 !important;
    transition: 1s ease-in;
  }

  .no-todos,
  .item-name {
    font-weight: bold;
    color: #fdf1b8;
  }

  #cards {
    width: 500px;
    margin: 0 auto;
  }

  #addCard {
    display: inline-flex;
    padding: 20px;
    margin-bottom: 20px;
  }

  .item-body {
    color: #fdf1b8;
    font-size: 12px;
  }

  .fa {
    height: 16px;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>

<main>
  <div class="container">
    <h1>{appName}</h1>
    <div id="addCard">
      <form on:submit|preventDefault={addCard}>
        <input bind:value={name} placeholder="Entrez un nom" />
        <input bind:value={body} placeholder="Entrez une description" />
        <button>Ajouter</button>
      </form>
    </div>
    {#if data.length === 0}
      <p class="no-todos">Il n'y a pas encore de todos !</p>
    {:else}
      <div id="cards">
        {#each data as item}
          <div
            id="card"
            class={classNames({ 'favorite-card': data[item.id - 1].favorite })}>
            <p class="item-name">{item.name}</p>
            <p class="item-body">{item.body}</p>
            <div class="favorite-button" on:click={toggleFavorite(item.id)}>
              {#if data[item.id - 1].favorite === true}
                <i class="fa fa-star" aria-hidden="true" />
              {:else}
                <i class="fa fa-star-o" aria-hidden="true" />
              {/if}
            </div>
            <div class="delete-button" on:click={deleteCard(item.id)}>
              <i class="fa fa-times" aria-hidden="true" />
            </div>
          </div>
        {/each}
      </div>
    {/if}
  </div>
</main>
