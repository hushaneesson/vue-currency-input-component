<template>
  <div class="p-4">

  <p class="text-center font-bold text-teal-700">Currency Input Tester</p>

   <div class="mt-5">
      <p class="text-teal-700 font-bold">Test Item</p>
      <input type="text" class="rounded-md border p-1 mr-2" placeholder="locale" v-model="testItem.locale"/>
      <input type="text" class="rounded-md border p-1" placeholder="currency" v-model="testItem.currency"/>


      <number-input class="mt-2" id="testItem" :locale="testItem.locale" :currency='testItem.currency' v-model="testItem.value" />
      <p class="test-sm text-gray-700">cents {{ testItem.value }}</p>
    <div>


    <div class="mt-5" v-for="item in testCases" :key="item.name">
      <p class="text-teal-700 font-bold">{{ item.name }}</p>
      <number-input :id="item.name" :locale="item.locale" :currency='item.currency' v-model="item.value" />
      <p class="test-sm text-gray-700 mt-3">org value {{ item.orgValue }}</p>
      <p class="test-sm text-gray-700">cents {{ item.value }}</p>
    <div>
  </div>
</template>

<script>
import NumberInput from './NumberInput.vue';
export default {
  components: {
    NumberInput,
  },

  data() {
    return {
      testItem:{},
      testCases: [
        {
          name: 'USD',
          orgValue: '1,234.569',
          value: '1,234.569',
          locale: 'en-US',
          currency: 'USD',
        },
        {
          name: 'DE-EUR',
          orgValue: '1.234,569',
          value: '1.234,569',
          locale: 'de-DE',
          currency: 'EUR',
        },
        {
          name: 'BHD',
          orgValue: '1٬234.569',
          value: '1٬234.569',
          locale: 'ar-BH',
          currency: 'BHD',
        },
        // {
        //   name: 'CHF',
        //   orgValue: '1٬234.569',
        //   value: '1٬234.569',
        //   locale: 'fr-CH',
        //   currency: 'CHF',
        // },
      ],
    };
  },
};
</script>
