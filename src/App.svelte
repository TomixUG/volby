<script>
  import { onMount } from "svelte";

  let data = [];
  let finishedPer;

  onMount(() => {
    fetchData();

    setInterval(fetchData, 5000);
  });

  async function fetchData() {
    let tmpdata = [];

    const response = await fetch("https://www.volby.cz/pls/prez2023/vysledky");
    const text = await response.text();
    const xmlDoc = new DOMParser().parseFromString(text, "text/xml");

    let d = xmlDoc
      .getElementsByTagName("CR")[0]
      .getElementsByTagName("UCAST")[0]
      .getAttribute("OKRSKY_ZPRAC_PROC");
    finishedPer = d;

    let z = xmlDoc
      .getElementsByTagName("CR")[0]
      .getElementsByTagName("KANDIDAT");

    for (let k = 0; k < z.length; k++) {
      let x = z[k];
      let jmeno = x.getAttribute("JMENO");
      let prijmeni = x.getAttribute("PRIJMENI");
      let procento = x.getAttribute("HLASY_PROC_1KOLO");

      tmpdata.push({
        jmeno: jmeno + " " + prijmeni,
        procento: parseFloat(procento),
      });

      tmpdata.sort((a, b) => {
        if (a.procento < b.procento) return 1;
        if (a.procento > b.procento) return -1;
        return 0; // No sorting
      });

      data = tmpdata; // this fixes updating
    }
    console.log(data);
  }
</script>

<main>
  <div>
    <h1>Volby 2023</h1>
    <button style="margin-bottom: 30px" on:click={fetchData}
      >Aktualizovat</button
    >
    <div style="margin-bottom: 30px">Sečteno: {finishedPer}%</div>

    <hr />
    {#each data as d}
      <div>
        <h2>{d.jmeno}</h2>
        <h4>{d.procento}%</h4>
      </div>
      <hr />
    {/each}
  </div>
</main>

<style>
</style>
