<script>
  import createSvelidation from "svelidation";
  import Thankyou from "./Thankyou.svelte";

  let success = false;

  const { createEntry, createForm } = createSvelidation({
    validateOnEvents: { input: true },
    presence: "required"
  });
  const [errorsF1, valueF1, inputF1] = createEntry({
    type: "string",
    required: true,
    min: 8,
    match: "(?=.*[0-9])(?=.*[A-Z])(?=.*[a-z])"
  });
  const [errorsF2, valueF2, inputF2] = createEntry({
    type: "string",
    required: true,
    equal: value => {
      return value === $valueF1;
    }
  });

  const onSuccess = values => {
    handleClickPassword();
  };

  export let token;

  function handleClickPassword() {
    const myHeaders = new Headers();
    myHeaders.append("Content-Type", "application/json");

    let raw = JSON.stringify({ password: $valueF2, token: token });

    const requestOptions = {
      method: "POST",
      headers: myHeaders,
      body: raw,
      redirect: "follow"
    };

    fetch(
      "https://api.comexposium-sso.com/_plugin/Comexposium/user/regeneratePassword",
      requestOptions
    )
      .then(response => response.text())
      .then(result => console.log(result))
      .then(result => (success = true))
      .catch(error => console.log("error", error));
  }
</script>

{#if !success}
  <div class="flex items-center justify-center">
    <div class="w-full max-w-xs">
      <form
        class="bg-white shadow-2xl rounded px-8 pt-6 pb-8 mb-4"
        on:submit|preventDefault
        use:createForm={{ onSuccess }}>
        <div class="mb-4">
          <label
            class="block text-gray-700 text-sm font-bold mb-2"
            for="password">
            Password
          </label>
          <input
            use:inputF1
            bind:value={$valueF1}
            class="shadow appearance-none border rounded w-full py-2 px-3
            text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
            id="password"
            type="password"
            placeholder="*****" />
          {#if $errorsF1.includes('required')}
            <span
              class="flex items-center bg-orange-500 text-white text-sm
              font-bold px-4 py-3"
              role="alert">
              This field is required
            </span>
          {/if}
          {#if $errorsF1.includes('match')}
            <span
              class="flex items-center bg-orange-500 text-white text-sm
              font-bold px-4 py-3"
              role="alert">
              Use at least 8 symbols with:
              <br />
              1 digit
              <br />
              1 upper case letter
              <br />
              1 lower case letter
            </span>
          {/if}
        </div>
        <div class="mb-4">
          <label
            class="block text-gray-700 text-sm font-bold mb-2"
            for="password2">
            Confirm you password
          </label>
          <input
            use:inputF2
            bind:value={$valueF2}
            class="shadow appearance-none border rounded w-full py-2 px-3
            text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
            id="password2"
            type="password"
            placeholder="*****" />
          {#if $errorsF2.includes('required')}
            <span
              class="flex items-center bg-orange-500 text-white text-sm
              font-bold px-4 py-3"
              role="alert">
              This field is required
            </span>
          {/if}
          {#if $errorsF2.includes('equal')}
            <span
              class="flex items-center bg-orange-500 text-white text-sm
              font-bold px-4 py-3"
              role="alert">
              Should be equal with second one
            </span>
          {/if}
        </div>
        <div class="flex items-center justify-center">
          <button
            class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4
            rounded focus:outline-none focus:shadow-outline"
            type="submit">
            Regenerate your password
          </button>
        </div>
      </form>
    </div>
  </div>
{/if}

{#if success}
  <Thankyou />
{/if}
