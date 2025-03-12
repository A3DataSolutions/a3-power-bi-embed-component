<script>

  import { getContext } from "svelte";
  import { onMount } from "svelte";


  export let accessToken;
  export let reportType;
  export let reportId;
  export let embedUrl;

  $: abbrReportType = (reportType == 'Paginated') ? 'rdlEmbed' : 'reportEmbed'

  const { styleable } = getContext("sdk");
  const component = getContext("component");
  import { tick } from 'svelte';

  
  import * as pbi from "powerbi-client";
  

  let powerbi = new pbi.service.Service(
    pbi.factories.hpmFactory,
    pbi.factories.wpmpFactory,
    pbi.factories.routerFactory,
  );


  $: (async () => {
    if (accessToken.length > 30) {
    await tick();
    try {
      powerbi.reset(reportContainer)
    }
    catch {
      console.log('No report loaded so not resettable')
    }
    let config = {
      type: "report",
      tokenType: pbi.models.TokenType.Embed,
      accessToken: accessToken,
      embedUrl: embedUrl ?? ("https://app.powerbi.com/"+abbrReportType),
      id: reportId,
      // permissions: pbi.models.Permissions.Read,
    };
    console.log('Writing report');
    console.log('getting reporting container'+"reportContainer"+($component.id))
    const reportContainer = document.getElementById("reportContainer"+($component.id));
    console.log('Finding report container')
    console.log(reportContainer)
    let report = powerbi.embed(reportContainer, config);
  }
  else {
    console.log('Waiting for Access Token')
  }
  })()

</script>

<div use:styleable={$component.styles}>
    <div id="reportContainer{$component.id}"></div>
</div>
