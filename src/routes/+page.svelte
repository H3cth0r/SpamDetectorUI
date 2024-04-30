<script lang="ts">
  import { FileDropzone } from '@skeletonlabs/skeleton';
  import { RadioGroup, RadioItem } from '@skeletonlabs/skeleton';

  let file_value: string = "json";
  let base_url: string = "localhost:8080/";
  let model_value: string = "roberta";

  let files: FileList;
  function addedFile(e: Event): void{
    console.log(files);
  }

  let request_body: string;
  let response_body: string;
  
  async function post_json () {
    let toSend;
    let contentType: string = "application/json";
    if(file_value === "csv"){
       let formData = new FormData();
       formData.append("file", files[0]);
      for(var pair of formData.entries()) {
         console.log(pair[0]+ ', '+ pair[1]); 
      }
       toSend = formData;
       contentType = "multipart/form-data";
    }else{
      toSend = JSON.stringify(JSON.parse(request_body));
    }
    console.log(file_value);
    const res =  await fetch(base_url + model_value + "/" + file_value, {
      method: 'POST',
      headers: {
        "Content-Type": contentType,
        "Accept": "*/*",
        "Accept-Encoding": "gzip, deflate, br"
      },
      body: toSend 
    })
    response_body = JSON.stringify(await res.json());
  }

  let open_warning: boolean = false;
  function close_warning(): void{
    open_warning=false; 
  }
  let greeting_open: boolean = true;
  function close_greeting(): void{
    greeting_open=false;
  }
</script>

<svelte:head>
	<link rel="stylesheet" href="https://unpkg.com/mono-icons@1.0.5/iconfont/icons.css" >
</svelte:head>

<!-- main div -->
<div
  class="w-screen h-screen"
>
  <div
    class="fixed top-3 right-3 z-50"
  >
    <a href="/swagger-ui" class="btn variant-filled">
      <!-- <span>(icon)</span> -->
      <span>Swagger</span>
    </a>
  </div>
  <!-- content div -->
  <div
    class="w-full h-full flex justify-center items-center"
  >
    <div
      class="card w-1/2 h-4/5 flex flex-col justify-center items-center"
    >

      <div
        class="h-3/4 w-2/3"
      >
        <label class="label h-1/6">
          <span>Base URL</span>
          <input class="input h-1/3" type="text" bind:value={base_url} placeholder="Input" />
        </label>

        <label class="label">
          <span>Model</span>
          <select class="select" bind:value={model_value}>
            <option value="roberta">Roberta</option>
            <option value="tiny-bert">Tiny Bert</option>
            <option value="xgb/w2v">XGB W2V</option>
            <option value="xgb/roberta">XGB Roberta</option>
          </select>
        </label>

        <div
        class="height_body_type flex flex-col justify-center"
        >
            <span>Body Type</span>
            <RadioGroup >
              <RadioItem bind:group={file_value} name="justify" value={"json"}>Json</RadioItem>
              <RadioItem bind:group={file_value} name="justify" value={"csv"}>File</RadioItem>
            </RadioGroup>
        </div>

        <div
          class="h-1/4 flex flex-col justify-center"
        >
          {#if file_value == "csv"}
              <FileDropzone name="files" bind:files on:change={addedFile} accept="text/csv">
              </FileDropzone>
          {:else}
            <textarea class="textarea" bind:value={request_body} rows="4" placeholder="Lorem ipsum dolor sit amet consectetur adipisicing elit." />
          {/if}
        </div>

        <div
          class="h-1/4 flex flex-col justify-center"
        >
          <span>Response</span>
          <textarea class="textarea" rows="4" bind:value={response_body} placeholder="Lorem ipsum dolor sit amet consectetur adipisicing elit." />
        </div>
      </div>

      <button on:click={post_json} type="button" class="btn variant-filled">Button</button>
    </div>
  </div>

  {#if open_warning == false && greeting_open==true}
    <div
      class="fixed bottom-0 left-0 right-0 z-50"
    >
      <aside class="alert variant-filled-primary">
          <!-- Icon -->
          <div style="font-size: 40px;">☝️&#129299;</div>
          <!-- Message -->
          <div class="alert-message">
              <h3 class="h3">Howdy Y'all!</h3>
              <p>Glad to have you here.</p>
          </div>
          <!-- Actions -->
          <div class="alert-actions">
            <button class="btn-icon variant-filled" on:click={close_greeting}><i class="fa-solid fa-xmark"/>X</button>
          </div>
      </aside>
    </div>
  {/if}

  {#if open_warning == true}
    <div
      class="fixed bottom-0 left-0 right-0 z-50"
    >
      <aside class="alert variant-filled-error" >
          <!-- Icon -->
          <i class="mi mi-warning" style="font-size: 30px;"></i>
          <!-- Message -->
          <div class="alert-message">
              <h3 class="h3">Error</h3>
              <p>There was an error. Please try again.</p>
          </div>
          <!-- Actions -->
          <div class="alert-actions">
            <button class="btn-icon variant-filled" on:click={close_warning}><i class="fa-solid fa-xmark"  />X</button>
          </div>
      </aside>
    </div>
  {/if}
    
</div>

<style>
  .height_navbar {
    height: 7%;
  }
  .height_body_type {
    height: 15%;
  }
</style>
