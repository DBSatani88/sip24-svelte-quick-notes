<script>
  import { onMount } from 'svelte';

  let pages = [];
  let currentPageIndex = 0;
  let title = '';
  let note = '';

  onMount(() => {
    const savePages = localStorage.getItem("pages");
    if (savePages) {
      pages = JSON.parse(savePages);
      title = pages[currentPageIndex];
      note = localStorage.getItem(title);
    } else {
      addPage();
    }
  });

  function saveNote() {
    const storedPageName = pages[currentPageIndex];
    if (storedPageName != title ) {
      localStorage.removeItem(storedPageName);
      pages[currentPageIndex] = title;
    }
    localStorage.setItem(title, note);
    localStorage.setItem('pages', JSON.stringify(pages));
  }

  function addPage() {
    pages.push("New Page");
    selectpage(pages.length ? pages.length - 1 : 0)
  }

  function deleteNote() {
    const storedPageName = pages[currentPageIndex];
    localStorage.removeItem(storedPageName);
    pages.splice(currentPageIndex, 1);
    if (pages.length > 0) {
      selectpage(Math.max(0, currentPageIndex - 1));
    } else {
      addPage();
    }
    localStorage.setItem("pages", JSON.stringify(pages));
  }

  function selectpage(index) {
    currentPageIndex = index;
    title = pages[currentPageIndex];
    note = localStorage.getItem(title); 
  }
</script>

<aside class="fixed top-0 left-0 z-40 w-60 h-screen">
<div class="bg-light-gray overflow-y-auto py-5 px-3 h-full border-r border-gray-200">
  <ul class="space-y-2">
    {#each pages as page, index}
      <li>
        <button on:click={()=>selectpage(index)} class="{index == currentPageIndex ? 'bg-dark-gray' : ''} py-2 px-3 text-gray-900 rounded-lg">{page}</button>
      </li>
    {/each}
    <li class="text-center"><button on:click={addPage} class="font-medium">+ Add Page</button></li>
  </ul>
</div>
</aside>

<main class="p-4 ml-60 h-auto">
  <div class="grid grid-cols-2 item-center mb-3"> 
    <h1 class="text-3xl font-bold" contenteditable bind:textContent={title}></h1>
    <div class="ml-auto">
      <button class="bg-gray-800 text-white px-5 py-2.5 rounded-lg font-medium text-sm  mt-3 hover:bg-gray-900" on:click={saveNote}> Save </button>
    <button class="bg-red-500 text-white px-5 py-2.5 rounded-lg font-medium text-sm mt-3 hover:bg-red-900 ml-2" on:click={deleteNote}> Delete </button>
    </div>
  </div>
  <hr/>
  <textarea class="mt-3 block w-full bg-gray-50 border border-gray-300 rounded-lg text-gray-900 p-2.5" bind:value={note}></textarea>  
</main>

<style>
.bg-light-gray {
  background: #FBFBFB;
}

.bg-dark-gray {
  background: #EFEFEF;
}
</style>