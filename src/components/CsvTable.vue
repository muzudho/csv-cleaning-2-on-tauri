<!-- src/components/CsvTable.vue -->
<template>
    <v-container>
        <v-btn color="primary" @click="loadCsv">CSVファイルを選択</v-btn>
        <v-data-table
            :headers="headers"
            :items="items"
            class="elevation-1 mt-4"
            v-if="items.length > 0"
        ></v-data-table>
    </v-container>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { open } from '@tauri-apps/api/dialog';
import { readTextFile } from '@tauri-apps/api/fs';

// テーブルのヘッダーとデータを保持
const headers = ref<{ text: string; value: string }[]>([]);
const items = ref<any[]>([]);

// CSVを読み込む
async function loadCsv() {
    try {
        // ファイル選択ダイアログ（.csvのみ）
        const selected = await open({
            filters: [{ name: 'CSV', extensions: ['csv'] }],
        });

        if (typeof selected === 'string') {
            const csvContent = await readTextFile(selected);
            const parsed = parseCsv(csvContent);
            headers.value = parsed.headers;
            items.value = parsed.items;
        }
    } catch (err) {
        console.error('CSV読み込みエラー:', err);
    }
}

// CSVをパース
function parseCsv(content: string) {
    const lines = content.trim().split('\n');
    const headers = lines[0].split(',').map((h, i) => ({
        text: h.trim(),
        value: `col${i}`,
    }));
    const items = lines.slice(1).map((line) => {
        const values = line.split(',');
        return headers.reduce((obj, header, i) => {
            obj[header.value] = values[i]?.trim() || '';
            return obj;
        }, {} as any);
    });
    return { headers, items };
}
</script>
