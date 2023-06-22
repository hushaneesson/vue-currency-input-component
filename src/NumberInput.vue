<template>
  <input
    class="
      block
      w-full
      max-w-sm
      rounded-md
      border
      p-1.5
      text-gray-900
      focus:none
    "
    type="text"
    v-model="formattedValue"
    @change="updateValue"
  />
</template>

<script>
import Dinero from 'dinero.js';
export default {
  props: {
    locale: {
      default: 'en-US',
      type: String,
    },

    currency: {
      default: 'JMD',
      type: String,
    },
    modelValue: {
      default: '0',
      type: [String, Number],
    },
    error: {
      default: false,
      type: Boolean,
    },
  },

  data() {
    return {
      valueAsCents: 0,
      formattedValue: this.modelValue,
    };
  },

  mounted() {
    if (Number.isInteger(+this.formattedValue)) {
      this.formattedValue = Dinero({
        amount: +this.formattedValue,
        currency: this.currency,
      })
        .setLocale(this.locale)
        .toFormat('0,0.00');
    } else {
      this.updateValue();
    }
  },

  methods: {
    updateValue() {
      this.valueAsCents = this.toCents(
        this.formattedValue,
        this.locale,
        this.currency
      );

      // error check
      if (!isNaN(this.valueAsCents)) {
        this.formattedValue = Dinero({
          amount: this.valueAsCents,
          currency: this.currency,
        })
          .setLocale(this.locale)
          .toFormat('0,0.00');
      }

      this.$emit('update:modelValue', this.valueAsCents);
    },

    toCents(formattedNumber) {
      // Create a formatter with the given locale and currency.
      const formatter = new Intl.NumberFormat(this.locale, {
        style: 'currency',
        currency: this.currency,
      });

      // Use the formatter to determine the decimal and grouping separators.
      const formattedParts = formatter.formatToParts(12345.6789); // Magic number

      let decimalSeparator = '.';
      let groupSeparator = ',';
      let currencySymbol = '';

      for (const part of formattedParts) {
        if (part.type === 'decimal') {
          decimalSeparator = part.value;
        } else if (part.type === 'group') {
          groupSeparator = part.value;
        } else if (part.type === 'currency') {
          currencySymbol = part.value;
        }
      }

      // Use resolvedOptions method to get the fraction digits for the current currency
      const fractionDigits = formatter.resolvedOptions().maximumFractionDigits;

      // Remove the currency symbol from the value
      const numberWithoutCurrencySymbol = formattedNumber.replace(
        new RegExp(`\\${currencySymbol}`, 'g'),
        ''
      );

      // Remove the grouping separator, replace the decimal separator with a period, parse as a float.
      const numberWithoutGroupSeparator = numberWithoutCurrencySymbol.replace(
        new RegExp(`\\${groupSeparator}`, 'g'),
        ''
      );
      const numberWithStandardDecimalSeparator =
        numberWithoutGroupSeparator.replace(decimalSeparator, '.');
      const numberAsFloat = parseFloat(numberWithStandardDecimalSeparator);

      // Custom rounding based on the locale and currency
      let numberInSmallestUnit;
      if (this.locale === 'fr-CH' && this.currency === 'CHF') {
        // TODO write script to parse this currency
        throw new Error(
          'This currency is not supported. Please contact administrator'
        );

        // Round to the nearest 5 cents
        numberInSmallestUnit =
          Math.round((numberAsFloat * Math.pow(10, fractionDigits)) / 5) * 5;
      } else {
        // General rounding
        numberInSmallestUnit = Math.round(
          numberAsFloat * Math.pow(10, fractionDigits)
        );
      }

      return numberInSmallestUnit;
    },
  },
};
</script>
