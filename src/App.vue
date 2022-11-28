<template>
    <div class="min-h-screen bg-slate-800 flex flex-col">
        <div class="mt-12 p-12 mx-auto w-full max-w-7xl bg-slate-100 rounded-lg">
            <h1 class="text-2xl inline-flex font-bold text-slate-700 bg-white rounded-xl p-5 shadow-lg">SEMRush API costs calculator</h1>
            <div>
                <div class="mt-12 flex justify-between">
                    <div class="flex text-slate-700">
                        <div>
                            Current cost: <span class="ml-3 font-bold text-lg text-amber-500">{{ Number(costs).toFixed(5) }}</span>
                        </div>
                        <div class="ml-5">
                            <span class="text-slate-500">Databases</span>
                            <input
                                type="number"
                                v-model="databases"
                                class="ml-3 w-12 border-0 border-b border-b-amber-500 bg-slate-100 text-slate-700 font-semibold focus:border-indigo-600 focus:ring-0"
                            />
                        </div>
                    </div>
                    <div class="flex items-center">
                        <span class="text-slate-500">API Unit price</span>
                        <input
                            type="number"
                            step="0.000001"
                            v-model="unitPrice"
                            class="ml-3 border-0 border-b border-b-amber-500 bg-slate-100 text-slate-700 font-semibold focus:border-indigo-600 focus:ring-0"
                        />
                    </div>
                </div>
            </div>
            <div class="mt-12">
                <h2 class="text-xl font-bold text-slate-700">Calls</h2>
                <div class="mt-6 space-y-4">
                    <div class="px-4 pb-1 grid grid-cols-4 border-b border-slate-300">
                        <div class="col-span-2">Type</div>
                        <div>Lines</div>
                    </div>
                    <div v-for="(call, index) in calls" :key="call.type.type" class="shadow">
                        <div class="p-4 grid grid-cols-4 bg-white rounded-lg">
                            <div class="col-span-2">
                                <div class="font-semibold">
                                    {{ call.type.name }}
                                </div>
                                <div class="mt-1 text-sm text-slate-500">
                                    {{ call.type.description }}
                                </div>
                                <a :href="call.type.link" class="text-xs text-blue-300">
                                    {{ call.type.link }}
                                </a>
                            </div>
                            <div>
                                <input
                                    type="number"
                                    v-model="call.quantity"
                                    class="px-2 border-0 border-b border-b-amber-500 text-slate-700 font-semibold focus:border-indigo-600 focus:ring-0"
                                    :max="call.type.max_database_results"
                                />
                            </div>
                            <div class="flex justify-end items-center">
                                <XMarkIcon
                                    class="h-5 w-5 cursor-pointer hover:text-amber-600 hover:font-black"
                                    @click="calls = calls.filter(item => item !== call)"
                                />
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import { computed, ref } from "vue";
import { XMarkIcon } from '@heroicons/vue/24/outline';

const types = {
    domain_ranks: {
        type: 'domain_ranks',
        name: 'Domain Overview',
        price: 10,
        max_database_results: 1,
        expected_results: 1,
        description: 'This report provides live or historical data on a domain’s keyword rankings in both organic and paid search in all regional databases.',
        link: 'https://developer.semrush.com/api/v3/analytics/overview-reports/#domain-overview-all-databases',
    },
    domain_organic: {
        type: 'domain_organic',
        name: 'Domain Organic Search Keywords',
        price: 10,
        max_database_results: 100000,
        expected_results: 1000,
        description: 'This report lists keywords that bring users to a domain via Google\'s top 100 organic search results.',
        link: 'https://developer.semrush.com/api/v3/analytics/domain-reports/#domain-organic-search-keywords',
    },
    phrase_all: {
        type: 'phrase_all',
        name: 'Keyword Overview',
        price: 10,
        max_database_results: 100000,
        expected_results: 1,
        description: 'This report provides a summary of a keyword, including its volume, CPC, competition, and the number of results in all regional databases.',
        link: 'https://developer.semrush.com/api/v3/analytics/keyword-reports/#keyword-overview-all-databases',
    }
}

const unitPrice = ref(0.0000225);
const databases = ref(1);

const calls = ref([
    {
        type: types.domain_ranks,
        quantity: types.domain_ranks.expected_results
    },
    {
        type: types.domain_organic,
        quantity: types.domain_organic.expected_results
    },
    {
        type: types.phrase_all,
        quantity: types.phrase_all.expected_results
    }
]);

const costs = computed(() => calls.value.reduce(
    (accumulator, currentValue) => accumulator + currentValue.quantity * currentValue.type.price,
    0
) * unitPrice.value * databases.value);

</script>
