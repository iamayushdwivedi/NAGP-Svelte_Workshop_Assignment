<svelte:options tag="my-file" />

<script>
    import { createEventDispatcher } from 'svelte';
  
    let name = '';
    let type = 'folder';
    let selectedFolder = '';
    let folders = [];
  
    const dispatch = createEventDispatcher();
  
    // Function to handle save event
    function handleSave() {
      if (name.trim() === '') {
        alert('Please enter a valid name.');
        return;
      }
  
      if (type === 'folder') {
        folders.push({ name, files: [], folders: [] });
      } else if (type === 'file') {
        const selectedFolderObj = findFolder(selectedFolder, folders);
        if (selectedFolderObj) {
          selectedFolderObj.files.push(name);
        }
      }
  
      resetForm();
    }
  
    // Function to handle cancel event
    function handleCancel() {
      resetForm();
    }
  
    // Function to find a folder by its path
    function findFolder(path, foldersArr) {
      if (path === '') {
        return null;
      }
  
      const pathArr = path.split('/');
      let currentFoldersArr = foldersArr;
  
      for (let i = 0; i < pathArr.length; i++) {
        const folderName = pathArr[i];
        const folderObj = currentFoldersArr.find(folder => folder.name === folderName);
  
        if (!folderObj || i === pathArr.length - 1) {
          return folderObj;
        }
  
        currentFoldersArr = folderObj.folders;
      }
  
      return null;
    }
  
    // Function to reset the form fields
    function resetForm() {
      name = '';
      type = 'folder';
      selectedFolder = '';
    }
  </script>
  
  <div class="container">
    <div class="input-group">
      <label for="name">Name:</label>
      <input type="text" id="name" bind:value={name} />
    </div>
    <div class="input-group">
      <label for="type">Type:</label>
      <select id="type" bind:value={type}>
        <option value="folder">Folder</option>
        <option value="file">File</option>
      </select>
    </div>
    {#if type === 'file'}
      <div class="input-group">
        <label for="folder">Folder:</label>
        <select id="folder" bind:value={selectedFolder}>
          <option value="">Select Folder</option>
          {#each folders as folderPath}
            <option value={folderPath}>{folderPath}</option>
          {/each}
        </select>
      </div>
    {/if}
    <div class="button-group">
      <button on:click={handleSave}>Save</button>
      <button on:click={handleCancel}>Cancel</button>
    </div>
    <div class="file-list">
      <h3>File/Folder Structure</h3>
      <ul>
        {#each folders as folder}
          <li>{folder.name}</li>
          {#each folder.files as file}
            <ul>
              <li>{file}</li>
            </ul>
          {/each}
          {#if folder.folders.length > 0}
            <ul>
              {#each folder.folders as subFolder}
                <li>{subFolder.name}</li>
                {#each subFolder.files as file}
                  <ul>
                    <li>{file}</li>
                  </ul>
                {/each}
              {/each}
            </ul>
          {/if}
        {/each}
      </ul>
    </div>
  </div>
  
  <style>
    .container {
      display: flex;
      flex-direction: column;
      align-items: center;
      margin-top: 50px;
    }
  
    .input-group {
      display: flex;
      margin-bottom: 10px;
    }
  
    .input-group label {
      margin-right: 10px;
    }
  
    .input-group select {
      margin-left: 10px;
    }
  
    .button-group {
      display: flex;
      justify-content: center;
      margin-top: 10px;
    }
  
    .file-list {
      margin-top: 20px;
    }
  </style>