<template>
    <!--
    <main class="container">
        <h1>Vuetify 3 の練習</h1>
    </main>
    -->
    <v-app>
        <v-main>
            <h1>Vuetify 3 の練習</h1>
    <!--
            <CsvTable />
    -->
            <v-container>
                <v-row>
                    <v-text-field v-model="filePathVM" label="File Path" style="width:55%; height: 10vh;"></v-text-field>
                    <v-btn @click="onOpenButtonClicked" style="width:15%; height: 10vh;">Open</v-btn>
                    <v-btn @click="onRefreshButtonClicked" style="width:15%; height: 10vh;">Refresh</v-btn>
                    <v-btn @click="onWriteButtonClicked" style="width:15%; height: 10vh;">Write</v-btn>
                </v-row>
                <textarea style="width:100%; height:80vh;" v-model="textVM"></textarea>
            </v-container>
        </v-main>
    </v-app>    
    <!--
    <main class="container">
        <div class="row">
            <select v-model="selectedItemVM" style="width:80%;">
                <option value="" selected></option>
                <option value="都道府県スプリット1">都道府県スプリット</option>
            </select>
            <button @click="onExecuteButtonClicked" style="width:20%; height: 10vh;">Execute</button>
        </div>
    </main>
    -->
</template>

<script setup lang="ts">
    //import { invoke } from "@tauri-apps/api/core";
    import { open } from '@tauri-apps/plugin-dialog';
    import { readTextFile, writeTextFile } from '@tauri-apps/plugin-fs';
    import { ref } from "vue";

    const filePathVM = ref("C:\\Users\\muzud\\OneDrive\\ドキュメント\\temp\\temp.csv");
    //const selectedItemVM = ref()
    const textVM = ref()

    async function onOpenButtonClicked() {
        console.log("［Open］ボタンを押したぜ。")
        // Open a dialog
        const filePath = await open({
            multiple: false,
            directory: false,  // ファイルを開く。
            defaultPath: filePathVM.value
        });
        filePathVM.value = filePath ?? "";  // パスの取得に失敗したら空文字列を入れる。
        readFile()
    }

    async function onRefreshButtonClicked() {
        console.log("［Refresh］ボタンを押したぜ。")
        readFile()
    }

    async function onWriteButtonClicked() {
        console.log("［Write］ボタンを押したぜ。")
        // 書き込むためには、📄 `src-tauri/capabilities/default.json` ファイルの `permissions` を設定する必要がある。
        await writeTextFile(filePathVM.value, textVM.value);
    }

    async function readFile() {
        const contents = await readTextFile(filePathVM.value);  
        textVM.value = contents
    }

    /*
    async function onExecuteButtonClicked() {
        console.log("［Execute］ボタンを押したぜ。")
        //textVM.value = `テスト中３ Execute selectedItemVM:[${selectedItemVM.value}]`
        // TODO 変換(textVM.value)
        textVM.value = await callTranslate(textVM.value, selectedItemVM.value)
    }

    // Tauriのコマンドを呼び出し。
    // 文字列を渡すと、指定の操作を実施後の文字列を返す。
    async function callTranslate(sourceStr: string, commandName: string): Promise<string> {
        const resultStr = await invoke<string>('translate', {sourceStr: sourceStr, commandName: commandName});
        return resultStr;
    }
    */
</script>

<style>
:root {
    font-family: Inter, Avenir, Helvetica, Arial, sans-serif;
    font-size: 16px;
    line-height: 24px;
    font-weight: 400;

    color: #0f0f0f;
    background-color: #f6f6f6;

    font-synthesis: none;
    text-rendering: optimizeLegibility;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    -webkit-text-size-adjust: 100%;
}

.container {
    height: 95vh;
    margin: 0;
    display: flex;
    flex-direction: column;
    justify-content: center;
    text-align: center;
}

.row {
    display: flex;
    justify-content: center;
}

input,
button {
    border-radius: 8px;
    border: 1px solid transparent;
    font-size: 1em;
    font-weight: 500;
    font-family: inherit;
    color: #0f0f0f;
    background-color: #ffffff;
    transition: border-color 0.25s;
    box-shadow: 0 2px 2px rgba(0, 0, 0, 0.2);
}

button {
    padding: 0.6em 1.2em;
    cursor: pointer;
}

button:hover {
    border-color: #396cd8;
}
button:active {
    border-color: #396cd8;
    background-color: #e8e8e8;
}

input,
button {
    outline: none;
}

@media (prefers-color-scheme: dark) {
    :root {
        color: #f6f6f6;
        background-color: #2f2f2f;
    }

    input,
    button {
        color: #ffffff;
        background-color: #0f0f0f98;
    }
    button:active {
        background-color: #0f0f0f69;
    }
}
</style>