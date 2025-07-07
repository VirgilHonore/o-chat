<script>
  import Icon from "@iconify/svelte";
  function openCloseMenu() {
    const salon = document.getElementById("nav_salon");
    salon.classList.toggle("open");
  }
</script>

<section class="main_chat">
  <nav>
    <div class="setting_icon">
      <button type="button" id="menu"
        ><Icon
          id="menu_burger"
          icon="typcn:th-menu"
          onclick={openCloseMenu}
        /></button
      >
      <button type="button" id="setting">
        <Icon id="gear" icon="typcn:cog" />
      </button>
    </div>
    <section id="nav_salon">
      <div class="onglet_salon">
        <h1>discution</h1>
        <button type="button" id="delete"
          ><Icon id="cross" icon="typcn:times-outline" /></button
        >
      </div>
      <div class="onglet_salon">
        <h1>di</h1>
        <button type="button" id="delete"
          ><Icon id="cross" icon="typcn:times-outline" /></button
        >
      </div>
      <div class="onglet_salon">
        <h1>discution</h1>
        <button type="button" id="delete"
          ><Icon id="cross" icon="typcn:times-outline" /></button
        >
      </div>
    </section>
  </nav>
  <section class="discution">
    <section class="question">
      <span class="id_user"
        >vous <br /><time datetime="">today 00:00</time></span
      >
      <p>
        Lorem ipsum dolor, sit amet consectetur adipisicing elit. Minus
        molestiae consequuntur obcaecati dolore dolorem velit voluptatum
        accusamus tempore qui eaque culpa, saepe corrupti quis odio commodi
        consequatur temporibus quasi odit. <br />Lorem ipsum dolor, sit amet
        consectetur adipisicing elit. Minus molestiae consequuntur obcaecati
        dolore dolorem velit voluptatum accusamus tempore qui eaque culpa, saepe
        corrupti quis odio commodi consequatur temporibus quasi odit.
      </p>
    </section>
    <section class="answer">
      <p>
        Lorem ipsum dolor, sit amet consectetur adipisicing elit. Minus
        molestiae consequuntur obcaecati dolore dolorem velit voluptatum
        accusamus tempore qui eaque culpa, saepe corrupti quis odio commodi
        consequatur temporibus quasi odit.
      </p>
    </section>
  </section>
</section>
<section class="input_user">
  <input type="text" placeholder="écrir ici" />
  <button type="submit" id="btn_sub"></button>
</section>

<style>
  :root {
    --blue-color: #237ac0;
    --red-color: #ff5656;
    --grey-color: #d9d9d9;
  }
  .main_chat {
    display: flex;
    flex-direction: column;
    height: 100vh;
  }
  nav {
    display: flex;
    flex-direction: column;
    margin-right: auto;
    padding: 10px 10px 10px 0;
    gap: 30px;
    background-color: var(--blue-color);
    border-radius: 0 0 20px 0;
    position: sticky;
    left: 0;
    top: 0;
    z-index: 99;
  }

  .setting_icon {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    margin: 0 5px;
    min-width: 30vw;
  }

  #menu,
  #setting,
  #delete {
    width: 40px;
    aspect-ratio: 1/1;
    background-color: var(--grey-color);
    border-radius: 50%;
  }
  #nav_salon {
    min-height: 30vh;
    max-height: 75vh;
    display: none;
  }

  .onglet_salon {
    display: flex;
    justify-content: space-between;
    background-color: var(--grey-color);
    border-radius: 0 20px 20px 0;

    max-width: 95%;
    min-height: 2.5em;
  }

  .onglet_salon h1 {
    margin: auto 0;
    padding: 0.3rem;
    font-size: 20px;
    font-weight: normal;
  }
  #menu :global(svg),
  #setting :global(svg),
  #delete :global(svg) {
    width: 80%;
    height: 80%;
    color: var(--blue-color);
  }

  .discution {
    position: fixed;
    top: 0;
    display: flex;
    flex-direction: column;
    padding: 20px 0;
    gap: 20px;
  }

  .question {
    display: flex;
    flex-direction: column;
    align-items: end;
    margin-left: auto;
    padding: 0.5em;
    background-color: var(--grey-color);
    max-width: 60vw;
    border-radius: 20px 0 0 20px;
  }

  .id_user {
    color: rgb(102, 102, 102);
  }

  .answer {
    background-color: var(--blue-color);
    padding: 0.5em;
    border-radius: 20px;
    margin: 0 10px;
  }

  .input_user {
    position: fixed;
    bottom: 0;
    display: flex;
    justify-content: space-between;
    width: 100%;
    background-color: var(--grey-color);
    border-radius: 20px 20px 0 0;
    padding: 10px;
    margin-top: 10px;
  }

  #btn_sub {
    background-image: url(../public/send.png);
    background-size: contain;
    height: 55px;
    aspect-ratio: 1/1;
  }

  #btn_sub:hover,
  #menu:hover,
  #setting:hover,
  #delete:hover {
    transform: scale(1.1);
    cursor: pointer;
    transition: transform 0.3s ease;
  }
  .open {
    display: flex;
    flex-direction: column;
    gap: 1em;
    width: 50vw;
  }
</style>
