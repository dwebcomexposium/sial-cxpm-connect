<script>
  import createSvelidation from "svelidation";
  import Thankyou from "./Thankyou.svelte";

  let success = false;

  function ToggleType() {
    var x = document.getElementById("password");
    if (x.type === "password") {
      x.type = "text";
    } else {
      x.type = "password";
    }
  }

  function toggleEye() {
    if (hideEye) {
      hideEye = false;
      ToggleType();
    } else {
      hideEye = true;
      ToggleType();
    }
  }

  let hideEye = false;

  const { createEntry, createForm } = createSvelidation({
    validateOnEvents: { change: true, input: true, blur: true },
    clearErrorsOnEvents: { reset: true, focus: true },
    presence: "required",
    listenInputEvents: 1
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
      .then(result => (success = true))
      .catch(error => console.log("error", error));
  }
</script>

{#if !success}
  <div class="flex items-center justify-center">
    <div class="w-full max-w-xs">
      {#if $errorsF1.includes('match') || $errorsF1.includes('min')}
        <span
          class="flex items-center bg-orange-500 text-white text-sm font-bold
          px-4 py-3"
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
          <div class="relative">
            <div
              class="absolute -mt-10 inset-y-0 right-0 pr-3 flex items-center
              text-sm leading-5">
              {#if hideEye}
                <svg
                  class="h-6 text-gray-700"
                  on:click={toggleEye}
                  fill="none"
                  xmlns="http://www.w3.org/2000/svg"
                  viewbox="0 0 576 512">
                  <path
                    fill="currentColor"
                    d="M572.52 241.4C518.29 135.59 410.93 64 288 64S57.68 135.64
                    3.48 241.41a32.35 32.35 0 0 0 0 29.19C57.71 376.41 165.07
                    448 288 448s230.32-71.64 284.52-177.41a32.35 32.35 0 0 0
                    0-29.19zM288 400a144 144 0 1 1 144-144 143.93 143.93 0 0
                    1-144 144zm0-240a95.31 95.31 0 0 0-25.31 3.79 47.85 47.85 0
                    0 1-66.9 66.9A95.78 95.78 0 1 0 288 160z" />
                </svg>
              {/if}
              {#if !hideEye}
                <svg
                  class="h-6 text-gray-700"
                  on:click={toggleEye}
                  fill="none"
                  xmlns="http://www.w3.org/2000/svg"
                  viewbox="0 0 640 512">
                  <path
                    fill="currentColor"
                    d="M320 400c-75.85 0-137.25-58.71-142.9-133.11L72.2
                    185.82c-13.79 17.3-26.48 35.59-36.72 55.59a32.35 32.35 0 0 0
                    0 29.19C89.71 376.41 197.07 448 320 448c26.91 0 52.87-4
                    77.89-10.46L346 397.39a144.13 144.13 0 0 1-26 2.61zm313.82
                    58.1l-110.55-85.44a331.25 331.25 0 0 0 81.25-102.07 32.35
                    32.35 0 0 0 0-29.19C550.29 135.59 442.93 64 320 64a308.15
                    308.15 0 0 0-147.32 37.7L45.46 3.37A16 16 0 0 0 23 6.18L3.37
                    31.45A16 16 0 0 0 6.18 53.9l588.36 454.73a16 16 0 0 0
                    22.46-2.81l19.64-25.27a16 16 0 0
                    0-2.82-22.45zm-183.72-142l-39.3-30.38A94.75 94.75 0 0 0 416
                    256a94.76 94.76 0 0 0-121.31-92.21A47.65 47.65 0 0 1 304
                    192a46.64 46.64 0 0 1-1.54 10l-73.61-56.89A142.31 142.31 0 0
                    1 320 112a143.92 143.92 0 0 1 144 144c0 21.63-5.29
                    41.79-13.9 60.11z" />
                </svg>
              {/if}
            </div>
          </div>
          {#if $errorsF1.includes('required')}
            <span
              class="flex items-center bg-red-600 text-white text-sm font-bold
              px-4 py-3"
              role="alert">
              This field is required
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
              class="flex items-center bg-red-600 text-white text-sm font-bold
              px-4 py-3"
              role="alert">
              This field is required
            </span>
          {/if}
          {#if $errorsF2.includes('equal')}
            <span
              class="flex items-center bg-red-600 text-white text-sm font-bold
              px-4 py-3"
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
