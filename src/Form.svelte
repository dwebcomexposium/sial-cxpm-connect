<script>
  import { emailValidator, requiredValidator } from "./validators.js";
  import { createFieldValidator } from "./validation.js";
  import Sentmessage from "./Sentmessage.svelte";
  import Sialheader from "./Sialheader.svelte";

  const [validity, validate] = createFieldValidator(
    requiredValidator(),
    emailValidator()
  );

  let email = null;
  let hideIt = false;
  const disabledButtonCss = "opacity-50 cursor-not-allowed";

  function handleClick() {
    const myHeaders = new Headers();
    myHeaders.append("Content-Type", "application/json");

    let raw = JSON.stringify({
      email: email,
      salon: "sial",
      sessionSalon: "sial_2020",
      language: "eng-GB",
      fromThirdParty: "88e4012a56c4a80dbcfe831fa21a453fca9da353"
    });

    const requestOptions = {
      method: "POST",
      headers: myHeaders,
      body: raw,
      redirect: "follow"
    };

    fetch(
      "https://api.comexposium-sso.com/_plugin/Comexposium/user/requestRegeneratePassword",
      requestOptions
    )
      .then(response => response.text())
      .then(result => (hideIt = true))
      .catch(error => console.log("error", error));
  }

  let params = new URLSearchParams(document.location.search);

  if (params.has("email")) {
    const emailParam = params.get("email");
    email = emailParam;
  }
</script>

<Sialheader/>

{#if hideIt}
  <Sentmessage email={email} />
{/if}

{#if !hideIt}
  <div class="flex items-center justify-center">
    <div class="w-full max-w-xs">
      <form class="bg-white shadow-2xl rounded px-8 pt-6 pb-8 mb-4">
        <div class="mb-4">
          <label class="block text-gray-700 text-sm font-bold mb-2" for="email">
            Email
          </label>
          <input
            bind:value={email}
            class="shadow appearance-none border rounded w-full py-2 px-3
            text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
            id="email"
            type="text"
            class:field-danger={!$validity.valid}
            class:field-success={$validity.valid}
            use:validate={email}
            placeholder="Email" />
          {#if $validity.dirty && !$validity.valid}
            <div
              class="flex items-center bg-orange-500 text-white text-sm
              font-bold px-4 py-3"
              role="alert">
              <svg
                class="fill-current w-4 h-4 mr-2"
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 20 20">
                <path
                  d="M12.432 0c1.34 0 2.01.912 2.01 1.957 0 1.305-1.164
                  2.512-2.679 2.512-1.269 0-2.009-.75-1.974-1.99C9.789 1.436
                  10.67 0 12.432 0zM8.309 20c-1.058
                  0-1.833-.652-1.093-3.524l1.214-5.092c.211-.814.246-1.141
                  0-1.141-.317 0-1.689.562-2.502 1.117l-.528-.88c2.572-2.186
                  5.531-3.467 6.801-3.467 1.057 0 1.233 1.273.705 3.23l-1.391
                  5.352c-.246.945-.141 1.271.106 1.271.317 0 1.357-.392
                  2.379-1.207l.6.814C12.098 19.02 9.365 20 8.309 20z" />
              </svg>
              <p>{$validity.message}</p>
            </div>
          {/if}
        </div>
        <div class="flex items-center justify-center">
          <button
            class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4
            rounded focus:outline-none focus:shadow-outline {!$validity.valid ? disabledButtonCss : ''}"
            disabled={!$validity.valid}
            on:click={handleClick}
            type="button">
            Regenerate your password
          </button>
        </div>
      </form>
      <p class="text-center text-gray-500 text-xs">
        You will receive an email to regenerate your password
      </p>
    </div>
  </div>
{/if}