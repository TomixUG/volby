<script>
  import { onMount } from "svelte";

  var data = [];
  var finishedPer = "Loading...";

  onMount(() => {
    fetchData();

    setInterval(fetchData, 5000);
  });

  async function fetchData() {
    var tmpdata = [];

    const response = await fetch("https://www.volby.cz/pls/prez2023/vysledky");
    const text = await response.text();
    var xmlDoc = new DOMParser().parseFromString(text, "text/xml");

    var d = xmlDoc
      .getElementsByTagName("CR")[0]
      .getElementsByTagName("UCAST")[1]
      .getAttribute("OKRSKY_ZPRAC_PROC");
    finishedPer = d;

    var z = xmlDoc
      .getElementsByTagName("CR")[0]
      .getElementsByTagName("KANDIDAT");

    for (var k = 0; k < z.length; k++) {
      var x = z[k];
      var jmeno = x.getAttribute("JMENO");
      var prijmeni = x.getAttribute("PRIJMENI");
      var procento = parseFloat(x.getAttribute("HLASY_PROC_2KOLO"));

      if (Number.isNaN(procento)) continue;

      tmpdata.push({
        jmeno: jmeno + " " + prijmeni,
        procento: procento,
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
