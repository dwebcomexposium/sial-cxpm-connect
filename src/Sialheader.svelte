<script>
  const svg = "/images/logo-sial.svg";
  let resetPassword = false;
  let setPassword = false;

  if (window.location.href.indexOf("reset-password") > -1) {
    resetPassword = true;
  }

  if (window.location.href.indexOf("first-login") > -1) {
    resetPassword = false;
    setPassword = true;
  }
</script>

<nav
  class="flex items-center justify-center bg-yellow-400 flex-wrap p-6 border-b-2
  border-solid border-grey">
  <div class="flex items-center flex-shrink-0 text-black mr-6">
    <svg
      style="background: url({svg}) center center/contain no-repeat;"
      class="fill-current h-8 mr-2"
      width="100"
      height="100" />
    <span class="font-semibold text-l tracking-tight">
      18 - 22 october 2020
      <br />
      <span class="text-m font-thin tracking-tight">PARIS NORD VILLEPINTE</span>
    </span>
  </div>
</nav>

{#if resetPassword}
  <h1 class="text-4xl text-gray-700 text-center my-10 tracking-tight">
    Reset your password
  </h1>
{:else if setPassword}
  <h1 class="text-4xl text-gray-700 text-center my-10 tracking-tight">
    Set your password
  </h1>
{:else}
  <h1 class="text-4xl text-gray-700 text-center my-10 tracking-tight">
    Reset your password
  </h1>
{/if}
