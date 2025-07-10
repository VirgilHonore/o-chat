<script>
  import { onMount } from "svelte";

  import Icon from "@iconify/svelte";

  onMount(firstverifToken);
  onMount(callPB);

  /*_________________gestion ouverture du menu________________________*/
  let salon;
  function openCloseMenu() {
    salon.classList.toggle("close");
  }

  /*____________________________dark mode_____________________________*/
  /*pourquoi il faut que la classe dark mode soit appliquée d'origine pour que ça fonctionne ?*/
  let main_page;
  function darkMode(event) {
    event.preventDefault();
    main_page.classList.toggle("dark_mode");
  }
  /*____________________________________________________________*/

  /*************************************vérif token********************************************/
  const localKey = localStorage.getItem("maCle");
  console.log("la clé local est " + localKey);

  let verif_Token;
  let tokenUser = $state("");
  /*__evite de toujours taper le token___*/
  async function firstverifToken() {
    if (localKey === null) {
      verif_Token.classList.remove("close");
    } else {
      /*pas sur que la securité sois optimal a verifier plus tard*/
      /*effectivement, il suffit d'ajouter la classe close manuellement dans la console*/
      verif_Token.classList.add("close");
    }
  }

  /*_____gestion du token______*/
  async function verifTokenOnSub(event) {
    event.preventDefault();

    const response = await fetch("https://api.mistral.ai/v1/chat/completions", {
      method: "post",
      headers: {
        Authorization: "Bearer " + tokenUser,
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        model: "mistral-small-latest",
        messages: [
          {
            /***************comme un ping mais en plus sympa**********/
            role: "system",
            content:
              "Tu es un assistant francophone, convivial et intelligent. Tu dois répondre naturellement en français, de manière amicale et concise, comme si tu étais un humain très sympa.",
          },
        ],
      }),
    });
    if (response.status === 200) {
      alert("id de la relation est écrit en Dur !!! ");
      localStorage.setItem("maCle", tokenUser);
      verif_Token.classList.toggle("close");
      savPB();
      /*pas sûr, mais ça doit planter si on passe 2 fois par ici en rentrant un nouveau token car Mistral n'aime pas avoir 2 messages système  */
    } else {
      alert("token non valide");
      tokenUser = "";
    }
  }

  /*_________________connexion Mistral________________________*/
  let timestampIA = $state(null); /*ia on verra plus tard*/

  let inputUser = $state("");

  let answerIa = $state("");

  let conversation = $state([]);

  /********************************comunication ia***********************************************************/
  async function sendToIa(event) {
    event.preventDefault();

    /*_______creation du message user_________*/
    const userTalk = {
      role: "user",
      content: inputUser,
    };
    conversation.push(userTalk);

    console.log(
      "conv avant envoi mistra",
      JSON.parse(JSON.stringify(conversation))
    );
    /*je sais que j'utilise ce bout de code à 2 endroits mais pas le temps pour le mettre dans une fonction. On verra demain, là il est l'heure d'une bonne bière*/
    const conversationClean = [];
    for (const talk of conversation) {
      if (talk.role && talk.content) {
        conversationClean.push({
          role: talk.role,
          content: talk.content,
        });
      }
    }

    /*____________envoi a ia______________________*/
    const response = await fetch("https://api.mistral.ai/v1/chat/completions", {
      method: "post",
      headers: {
        Authorization: "Bearer " + localKey,
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        model: "mistral-small-latest",
        messages: conversationClean,
      }),
    });

    const result = await response.json();

    /*_______creation du message ia_________*/
    /*result.choices[0].message.content; a etait donné par l ia*/
    answerIa = result.choices[0].message.content;

    const iaTalk = {
      role: "assistant",
      content: answerIa,
    };
    conversation.push(iaTalk);

    /*_________________sauvegarde PB_______________________*/

    savPB(userTalk);
    savPB(iaTalk);

    inputUser = "";
  }

  /*****************************************communication avec pocket base*********************************************/

  /*__________recuperation de conversation via pocket base_______________*/
  async function callPB() {
    const responsePBBrut = await fetch(
      "http://127.0.0.1:8090/api/collections/discution/records",
      {
        headers: {
          "Content-Type": "application/json",
        },
      }
    );

    const responsePBTrad = await responsePBBrut.json();

    function cleanCallPB() {
      for (const talk of responsePBTrad.items) {
        if (talk.role && talk.content) {
          conversation.push({
            role: talk.role,
            content: talk.content,
          });
        }
      }
    }
    cleanCallPB();

    console.log(
      "converstion fin callpb brut",
      JSON.parse(JSON.stringify(responsePBTrad.items))
    );
    console.log(
      "converstion fin callpb",
      JSON.parse(JSON.stringify(conversation))
    );
  }
  /*_____________________gestion des salon___________________________*/
  let idSalontest = $state("");

  function gestionIdSalon(idSalon) {
    idSalontest = idSalon;
  }

  $effect(() => {
    console.log("idSalontest :", idSalontest);
  });

  /*________envoi de conversation a pocket base___________*/

  async function savPB(Talk) {
    Talk.salon = idSalontest;

    console.log("talk", JSON.parse(JSON.stringify(Talk)));
    await fetch("http://127.0.0.1:8090/api/collections/discution/records", {
      method: "post",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(Talk),
    });
  }
</script>

<!-- ________________^^__JS___^^________________ -->

<!-- ________________vv__html___vv________________ -->

<section class="main_chat dark_mode" bind:this={main_page}>
  <!-- _______nav_________ -->
  <nav>
    <div class="setting_icon">
      <button type="button" id="menu" onclick={openCloseMenu}
        ><Icon id="menu_burger" icon="typcn:th-menu" /></button
      >
      <button type="button" id="setting">
        <Icon icon="typcn:document-add" />
      </button>
      <button type="button" id="setting">
        <Icon id="gear" icon="typcn:cog" />
      </button>
      <button type="button" id="setting">
        <Icon id="dark-mode" icon="typcn:adjust-contrast" onclick={darkMode} />
      </button>
    </div>
    <ul bind:this={salon} class="nav_salon close">
      <li class="onglet_salon">
        <button
          type="button"
          class="salon"
          onclick={() => gestionIdSalon("3sslay2ago081cj")}
          ><h2>salon 1</h2>
        </button>
        <button type="button" class="delete">
          <Icon id="cross" icon="typcn:times-outline" />
        </button>
      </li>
      <li class="onglet_salon">
        <button
          type="button"
          class="salon"
          onclick={() => gestionIdSalon("3fp34okoiut5an5")}
          ><h2>salon 2</h2>
        </button>
        <button type="button" class="delete">
          <Icon id="cross" icon="typcn:times-outline" />
        </button>
      </li>
      <li class="onglet_salon">
        <button
          type="button"
          class="salon"
          onclick={() => gestionIdSalon("7rxpkfdbh4exzf3")}
          ><h2>salon 3</h2>
        </button>
        <button type="button" class="delete">
          <Icon id="cross" icon="typcn:times-outline" />
        </button>
      </li>
      <li class="onglet_salon">
        <button
          type="button"
          class="salon"
          onclick={() => gestionIdSalon("7nt1th2n7bzgp12")}
          ><h2>salon 4</h2>
        </button>
        <button type="button" class="delete">
          <Icon id="cross" icon="typcn:times-outline" />
        </button>
      </li>
    </ul>
  </nav>
  <!-- _______nav_________ -->

  <!-- ______Chat_________ -->
  <!--creation maison apres avoir compris la logique-->
  <!--du coup le probleme de l affichage  du prompt systeme est resolu-->
  <section class="discussion">
    {#each conversation as Exchanges}
      {#if Exchanges.role === "user"}
        <section class="question">
          <span>vous</span>
          <p>{Exchanges.content}</p>
        </section>
      {/if}
      {#if Exchanges.role === "assistant"}
        <section class="answer">
          <span>mistral</span>
          <p>{Exchanges.content}</p>
        </section>
      {/if}
    {/each}
  </section>
</section>
<!-- ______Chat_________ -->

<!-- ______input_________ -->
<form class="terminal_user" onsubmit={sendToIa}>
  <textarea
    name="input_user"
    id="input_user"
    placeholder="Écris ici"
    bind:value={inputUser}
  ></textarea>
  <input type="submit" value="" id="btn_sub" />
</form>

<!-- ______input_________ -->

<!-- ______verif token_________ -->
<section class="verifToken" bind:this={verif_Token}>
  <form class="formToken" onsubmit={verifTokenOnSub}>
    <input
      bind:value={tokenUser}
      type="text"
      class="inputToken"
      placeholder="Merci de renseigner votre token mistral ici"
    /><input type="submit" class="subToken" value="soumettre" />
  </form>
</section>

<!-- ________________^^__html___^^________________ -->

<!-- ________________vv__CSS___vv________________ -->

<style>
  /*attention du css a ete ajouté a normalize*/

  :root {
    --blue-color: #237ac0;
    --red-color: #ff5656;
    --grey-color: #d9d9d9;
    --dark-mode: #282c34;
  }

  .main_chat {
    display: flex;
    flex-direction: column;
    height: 100vh;
    position: relative;
    background-color: white;
  }
  /*pourquoi il faut que la classe dark mode soit appliquée d origine pour que ca fonctionne*/
  .dark_mode {
    background-color: var(--dark-mode);
  }

  nav {
    display: flex;
    flex-direction: column;
    margin-right: auto;
    padding: 10px 10px 10px 0;
    gap: 30px;
    background-color: var(--blue-color);
    border-radius: 0 0 20px 0;
    position: absolute;
    left: 0;
    top: 0;
    z-index: 99;
    min-width: 150px;
    box-shadow: 0 4px 4px 6px rgba(0, 0, 0, 0.2);
  }

  .setting_icon {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    margin: 0 5px;
    gap: 10px;
  }

  #menu,
  #setting,
  .delete {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 40px;
    aspect-ratio: 1/1;
    background-color: var(--grey-color);
    border-radius: 50%;
  }
  .nav_salon {
    display: flex;
    flex-direction: column;
    width: 200px;
    gap: 1em;
    min-height: 30vh;
  }

  .onglet_salon {
    display: flex;
    justify-content: space-between;
    background-color: var(--grey-color);
    border-radius: 0 20px 20px 0;
    min-height: 2.5em;

    .salon {
      width: 100%;
      border-radius: 0 20px 20px 0;
    }

    h2 {
      margin: auto 0;
      padding: 0.3rem;
      font-size: 20px;
      font-weight: normal;
    }
  }

  #menu :global(svg),
  #setting :global(svg),
  .delete :global(svg) {
    width: 80%;
    height: 80%;
    color: var(--blue-color);
  }

  .discussion {
    position: relative;
    top: 0;
    right: 0;
    display: flex;
    flex-direction: column;
    padding: 20px 0;
    gap: 20px;
    width: 100%;
    height: 90vh;
    overflow-y: auto;
  }

  .question {
    display: flex;
    flex-direction: column;
    align-items: end;
    margin-left: auto;
    padding: 0.5em;
    background-color: var(--grey-color);
    max-width: calc(100% - 10px);
    /* max-width: 60vw; */
    border-radius: 20px 0 0 20px;
  }

  .id_user {
    color: rgb(102, 102, 102);
  }

  .answer {
    color: var(--grey-color);
    background-color: var(--blue-color);
    padding: 0.5em;
    border-radius: 20px;
    margin: 0 10px;
    margin-right: auto;
    max-width: calc(100% - 20px);
  }

  .terminal_user {
    position: absolute;
    bottom: 0;
    /* la cest cree par IA jai pas compris je reviendrai dessus plus tard*/
    left: 50%;
    transform: translateX(-50%);
    /*______________________________________________________________*/
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 100%;
    background-color: var(--grey-color);
    border-radius: 20px 20px 0 0;
    padding: 10px;
    margin-top: 10px;
    max-width: 900px;
  }

  #input_user {
    margin: 1em;
    width: 100%;
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
  .delete:hover,
  .subToken:hover,
  .salon:hover {
    transform: scale(1.1);
    cursor: pointer;
    transition: transform 0.3s ease;
  }

  .verifToken {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100vw;
    height: 100vh;
    background-color: var(--blue-color);
    position: fixed;
    top: 0;
    z-index: 199;
  }

  .formToken {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 10px;
    width: 300px;
    height: 200px;
    border: var(--red-color) solid 4px;
    background-color: #d9d9d9;
    border-radius: 20px;
  }

  .inputToken {
    background-color: white;
    width: 90%;
    height: 30%;
    border-radius: 10px;
  }

  .subToken {
    background-color: var(--blue-color);
    padding: 10px;
    border-radius: 10px;
    color: var(--grey-color);
  }
  .close {
    display: none;
  }

  /* ___________  passage en descktop ___________ */
  @media screen and (min-width: 900px) {
    .main_chat {
      flex-direction: row;
    }

    nav {
      position: relative;
      height: 80vh;
      min-width: 210px;
      width: 210px;
      z-index: auto;
    }

    .nav_salon {
      display: flex;
      flex-direction: column;
    }

    .discussion {
      position: relative;
    }

    #menu {
      display: none;
    }
  }
</style>
