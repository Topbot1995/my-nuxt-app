<template>
    <div class="overflow-x-auto relative shadow-md sm:rounded-lg">
        <p>Please select the Data File(extension: .txt).</p>
        <input type="file" ref="doc" @change="readFile()" class="py-5" />
        <table
            class="w-full text-sm text-left text-gray-500 dark:text-gray-400"
        >
            <thead
                class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400"
            >
                <tr>
                    <th scope="col" class="py-3 px-6">日時</th>
                    <th scope="col" class="py-3 px-6">データA</th>
                    <th scope="col" class="py-3 px-6">データB</th>
                    <th scope="col" class="py-3 px-6">データC</th>
                    <th scope="col" class="py-3 px-6">データD</th>
                </tr>
            </thead>
            <tbody v-for="row in showData" :key="row.DateTime">
                <tr
                    class="bg-white border-b dark:bg-gray-800 dark:border-gray-700"
                >
                    <td class="py-4 px-6">
                        {{ row.DateTime }}
                    </td>
                    <td class="py-4 px-6">{{ row.DataA }}</td>
                    <td class="py-4 px-6">{{ row.DataB }}</td>
                    <td class="py-4 px-6">{{ row.DataC }}</td>
                    <td class="py-4 px-6">{{ row.DataD }}</td>
                </tr>
            </tbody>
        </table>
        <nav
            class="flex justify-between items-center pt-4"
            aria-label="Table navigation"
        >
            <span class="text-sm font-normal text-gray-500 dark:text-gray-400"
                >Showing
                <span class="font-semibold text-gray-900 dark:text-gray-700"
                    >{{ page * 10 + 1 }}~{{ (page + 1) * 10 }}</span
                >
                of
                <span class="font-semibold text-gray-900 dark:text-gray-700"
                    >{{data.length}}</span
                ></span
            >
            <ul class="inline-flex items-center -space-x-px">
                <li>
                    <a
                        href="javascript:void(0);"
                        class="block py-2 px-3 ml-0 leading-tight text-gray-500 bg-white rounded-l-lg border border-gray-300 hover:bg-gray-100 hover:text-gray-700 dark:bg-gray-800 dark:border-gray-700 dark:text-gray-400 dark:hover:bg-gray-700 dark:hover:text-white"
                        @click="goToPage(0)"
                    >
                        <span class="sr-only">Previous</span>
                        <svg
                            class="w-5 h-5"
                            aria-hidden="true"
                            fill="currentColor"
                            viewBox="0 0 20 20"
                            xmlns="http://www.w3.org/2000/svg"
                        >
                            <path
                                fill-rule="evenodd"
                                d="M12.707 5.293a1 1 0 010 1.414L9.414 10l3.293 3.293a1 1 0 01-1.414 1.414l-4-4a1 1 0 010-1.414l4-4a1 1 0 011.414 0z"
                                clip-rule="evenodd"
                            ></path>
                        </svg>
                    </a>
                </li>
                <li v-for="index in totalPages" :key="index">
                    <a
                        href="javascript:void(0);"
                        :class="{'py-2 px-3 leading-tight text-gray-500 bg-white border border-gray-300 hover:bg-gray-100 hover:text-gray-700 dark:bg-gray-800 dark:border-gray-700 dark:text-gray-400 dark:hover:bg-gray-700 dark:hover:text-white': true, 'bg-gray-700 text-gray-400' : index == page}"
                        @click="ChangeIndex(index)"
                        >{{ index }}</a
                    >
                </li>
                <li>
                    <a
                        href="javascript:void(0);"
                        class="block py-2 px-3 leading-tight text-gray-500 bg-white rounded-r-lg border border-gray-300 hover:bg-gray-100 hover:text-gray-700 dark:bg-gray-800 dark:border-gray-700 dark:text-gray-400 dark:hover:bg-gray-700 dark:hover:text-white"
                        @click="goToPage(1)"
                    >
                        <span class="sr-only">Next</span>
                        <svg
                            class="w-5 h-5"
                            aria-hidden="true"
                            fill="currentColor"
                            viewBox="0 0 20 20"
                            xmlns="http://www.w3.org/2000/svg"
                        >
                            <path
                                fill-rule="evenodd"
                                d="M7.293 14.707a1 1 0 010-1.414L10.586 10 7.293 6.707a1 1 0 011.414-1.414l4 4a1 1 0 010 1.414l-4 4a1 1 0 01-1.414 0z"
                                clip-rule="evenodd"
                            ></path>
                        </svg>
                    </a>
                </li>
            </ul>
        </nav>
    </div>
</template>
<script>
export default {
    name: "Table",
    data() {
        return {
            data: [],
            showData: [],
            page: 0,
            totalPages: 0,
        };
    },
    methods: {
        readFile() {
            this.file = this.$refs.doc.files[0];
            const reader = new FileReader();
            if (this.file && this.file.name.includes(".txt")) {
                reader.onload = (res) => {
                    const data = JSON.parse(res.target.result);
                    this.totalPages = Math.round(data.length / 10);
                    this.data = data;
                    this.showData = this.data.slice(
                        this.page * 10,
                        (this.page + 1) * 10
                    );
                };
                reader.onerror = (err) => console.log(err);
                reader.readAsText(this.file);
            } else {
                this.content = null;
                reader.onload = (res) => {
                    console.log(res.target.result);
                };
                reader.onerror = (err) => console.log(err);
                reader.readAsText(this.file);
            }
        },
        ChangeIndex(index) {
            this.page = index - 1;
            this.showData = this.data.slice(
                this.page * 10,
                (this.page + 1) * 10
            );
        },
        goToPage(type) {
            if (type && this.totalPages > this.page) {
                this.page++;
            } else {
                this.page ? this.page-- : 0;
            }

            this.showData = this.data.slice(
                this.page * 10,
                (this.page + 1) * 10
            );
        },
    },
};
</script>
