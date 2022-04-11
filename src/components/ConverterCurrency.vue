<template>
  <div class="converter">
    <el-row :gutter="12">
      <el-col :span="8" :xl="6" :lg="6" :md="8" :sm="12" :xs="23">
        <el-card shadow="always">
          <div class="converter__group">
            <div class="converter__info">
              <p class="converter__title">Amount i have</p>
              <p class="converter__course">
                <el-skeleton v-if="loader" :rows="0" variant="p" animated />
                <span v-else>
                  {{ parseFloat(cryptoCourse).toFixed(1) }} {{ currencyCurrentSelected }}
                </span>
                <el-popover
                  placement="left"
                  title="Важное сообщение"
                  :width="200"
                  trigger="hover"
                  content="Здесь указана стоимость 1 единицы валюты в долларах или евро. Например продавая 1BTC вы получите сумму равную текущему курсу минус 1% комиссии."
                >
                  <template #reference>
                    <img
                      :src="require('@/assets/images/question.svg')"
                      class="converter__question"
                    >
                  </template>
                </el-popover>
              </p>
            </div>
            <div class="group separator">
              <div class="group">
                <img
                  class="converter__icon"
                  :src="getIconByCrypto.icon"
                  :alt="getIconByCrypto.name"
                >
                <el-select v-model="cryptoCurrentSelected">
                  <el-option
                    v-for="currency in cryptoCurrencies"
                    :key="currency.name"
                    :value="currency.name"
                  >
                    <img class="el-select-dropdown__icon" :src="currency.icon" :alt="currency.name">
                    <span>{{ currency.name }}</span>
                  </el-option>
                </el-select>
              </div>
              <el-input
                type="number"
                :disabled="loader"
                @keyup="updateCryptoValue"
                v-model="cryptoCurrentValue"
                placeholder="For example: 1"
              />
            </div>
          </div>

          <div class="converter__group">
            <div class="converter__info">
              <p class="converter__title">I need</p>
            </div>
            <div class="group separator">
              <div class="group">
                <img
                  class="converter__icon"
                  :src="getIconByCurrency.icon"
                  :alt="getIconByCurrency.name"
                >
                <el-select v-model="currencyCurrentSelected">
                  <el-option
                    v-for="currency in currencies"
                    :key="currency.name"
                    :value="currency.name"
                  >
                    <img class="el-select-dropdown__icon" :src="currency.icon" :alt="currency.name">
                    <span>{{ currency.name }}</span>
                  </el-option>
                </el-select>
              </div>
              <el-input
                  type="number"
                  :disabled="loader"
                  v-model="currencyCurrentValue"
                  @keyup="updateCurrencyValue"
                  placeholder="For example: 100"
                />
            </div>
          </div>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import { computed, onMounted, ref, watch } from 'vue';
import axios from 'axios';

export default {
  name: 'ConverterCurrency',
  setup() {
    const cryptoCurrencies = ref([
      {icon: require('@/assets/images/btc.svg'), name: "BTC"},
      {icon: require('@/assets/images/eth.svg'), name: "ETH"}
    ]);
    const currencies = ref([
      {icon: require('@/assets/images/usd.svg'), name: "USD"},
      {icon: require('@/assets/images/eur.svg'), name: "EUR"}
    ]);
    const cryptoCurrentSelected = ref(cryptoCurrencies.value[0].name);
    const currencyCurrentSelected = ref(currencies.value[0].name);
    const cryptoCurrentValue = ref(1);
    const currencyCurrentValue = ref("");
    const cryptoCourse = ref("");
    const apiUrl = ref('https://api.kraken.com/0/public/Ticker?pair=');
    const loader = ref(false);

    const updateCryptoValue = (event) => {
      if (event) {
        const value = event.target.value;
        cryptoCurrentValue.value = value;

        currencyCurrentValue.value =
          Math.abs(parseFloat((value * cryptoCourse.value) - (value * cryptoCourse.value) * 0.01).toFixed(4));
      } else {
        currencyCurrentValue.value =
          Math.abs(parseFloat((cryptoCurrentValue.value * cryptoCourse.value) - (cryptoCurrentValue.value * cryptoCourse.value) * 0.01).toFixed(4));

      }
    }

    const updateCurrencyValue = (event) => {
      if (event) {
        const value = event.target.value;
        currencyCurrentValue.value = value;

        cryptoCurrentValue.value =
          Math.abs(parseFloat((value / (cryptoCourse.value - (cryptoCourse.value * 0.01)))).toFixed(4));
      } else {
        cryptoCurrentValue.value =
          Math.abs(parseFloat((currencyCurrentValue.value / (cryptoCourse.value - (cryptoCourse.value * 0.01)))).toFixed(4));
      }
    }

    const getCryptoCourse = async () => {
      loader.value = true;
      await axios.get(`${apiUrl.value}${cryptoCurrentSelected.value}${currencyCurrentSelected.value}`)
      .then(({data}) => {
        const keys = Object.keys(data.result);
        cryptoCourse.value = data.result[keys[0]].o;

        updateCryptoValue();
        updateCurrencyValue();

        loader.value = false;
      })
      .catch((err) => {
        loader.value = false;
        console.log(err);
      })
    }

    const getIconByCrypto = computed(() => {
      return cryptoCurrencies.value.find((valute) => valute.name === cryptoCurrentSelected.value);
    });

    const getIconByCurrency = computed(() => {
      return currencies.value.find((valute) => valute.name === currencyCurrentSelected.value);
    });

    watch([cryptoCurrentSelected, currencyCurrentSelected], () => {
      getCryptoCourse();
    });

    onMounted(() => {
      getCryptoCourse();
    });

    return {
      cryptoCurrencies,
      currencies,
      cryptoCurrentSelected,
      currencyCurrentSelected,
      cryptoCurrentValue,
      currencyCurrentValue,
      cryptoCourse,
      apiUrl,
      loader,

      updateCryptoValue,
      updateCurrencyValue,
      getCryptoCourse,

      getIconByCrypto,
      getIconByCurrency
    }
  }
};
</script>

<style scoped>
  .converter {
    margin-top: 50px;
  }

  .converter__info {
    display: flex;
    justify-content: space-between;
    margin-bottom: 10px;
  }

  .converter__title {
    font-weight: 500;
  }

  .converter__course {
    color: #a3a2a2;
    display: flex;
    align-items: center;
  }

  .el-row {
    justify-content: center;
  }

  .el-select-dropdown__item {
    display: flex;
    align-items: center;
  }

  .el-select-dropdown__icon {
    width: 20px;
    height: 20px;
  }

  .el-select {
    width: min-content;
    min-width: 75px;
  }

  .converter__icon {
    width: 20px;
  }

  .el-input {
    width: 40%;
  }

  .converter__question {
    width: 15px;
    margin-left: 10px;
  }

  .converter__group:not(:last-child) {
    margin-bottom: 20px;
  }

  .el-skeleton {
    min-width: 94px;
    height: 22px;
  }

  .el-skeleton >>> .el-skeleton__item {
    width: 100%;
    height: 100%;
  }
</style>
