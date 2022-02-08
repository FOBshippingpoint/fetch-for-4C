<script>
  const url = "https://immgt-fetcher.herokuapp.com";
  let started = false;
  let taskId;
  let results = [];
  let onlineCount = 1;

  async function crawl() {
    try {
      const response = await fetch(url + "/crawl");
      const data = await response.json();
      if (data.newsLink) {
        clearInterval(taskId);
      }
      onlineCount = data.onlineCount ? data.onlineCount : 1;
      results = [...results, data];
    } catch (error) {
      results = [...results, error];
    }
  }

  function handleSwitch() {
    started = !started;
    if (started) {
      crawl();
      taskId = setInterval(crawl, 1000 * 60);
      return;
    }

    clearInterval(taskId);
  }
</script>

<main class="container">
  <h1><ch>研究所備取爬蟲</ch></h1>
  <div class="terminal-card">
    <div>
      <ch>
        我的朋友現在正在等待研究所備取名額，所以我寫了一個爬蟲程式來看他會不會上。<br
        />爬蟲會每分鐘更新一次，按下按鈕開始爬蟲。
        <br />
        <br />
        <ins>現在有{onlineCount}人在線上等待結果</ins>
      </ch>
    </div>
  </div>
  <section>
    <blockquote>
      {#each results as result}
        {#if result.newsLink}
          <div>
            {result.message}{" "}<b
              >link: <a href={result.newsLink}>{result.newsLink}</a></b
            >{" "}
            <i
              >job status: {result.jobStatus}; last update: {new Date(
                parseInt(result.lastUpdate, 10)
              ).toLocaleTimeString()}</i
            >
          </div>
        {:else}
          <div>
            {result.message}{" "}
            <i
              >job status: {result.jobStatus}
              {#if result.lastUpdate}
                ; last update: {new Date(
                  parseInt(result.lastUpdate, 10)
                ).toLocaleTimeString()}
              {/if}
            </i>
          </div>
        {/if}
      {/each}
    </blockquote>
  </section>
  <button
    class="btn btn-default"
    name="submit"
    id="submit"
    on:click={handleSwitch}>{started ? "STOP" : "START"}</button
  >
</main>

<svelte:head>
  <meta
    http-equiv="Content-Security-Policy"
    content="connect-src {url} 'self'; child-src 'none'; object-src 'none'"
  />
</svelte:head>

<style>
  ch {
    font-family: "Noto Sans TC", sans-serif;
  }
</style>
