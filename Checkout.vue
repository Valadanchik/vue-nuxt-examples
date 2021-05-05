<template>
  <div>
    <div class="page-title-wrapper">
      <h1 class="page-title title-bordered-active">
        <span class="base">Checkout</span>
      </h1>
    </div>
    <div id="checkout" v-if="!isThankYou">
      <div class="checkout">
        <div class="checkout__main">
          <div class="container-1120">
            <section class="ckeckout-content-wrapper">
              <div class="checkout-step checkout-step-1">
                <div class="flex">
                  <MobileSummary
                    :products="products"
                    :cart="cart"
                  ></MobileSummary>
                  <ChooseStore
                    :chooseStore="chooseStore"
                    @openChooseStore="openChooseStore"
                    :storeDetails="storeDetails"
                    :chosenStore="chosenStore"
                    :shops="shops"
                    @openDetails="openDetails"
                  ></ChooseStore>
                  <div class="opc-wrapper checkout-left-wrapper checkout-payment-method show">
                    <div class="opc" id="checkoutSteps">
                      <nuxt-child
                        :zoom="zoom"
                        :controls="controls"
                        :coords="coords"
                        :placemarks="shippingStore"
                        :balloonType="balloonType"
                        @openChooseStore="openChooseStore"
                        :shippingMethod="shippingMethod"
                        @changeShippingMethod="changeShippingMethod"
                      />
                    </div>
                  </div>
                  <div class="checkout-right-wrapper">
                    <aside
                      class="modal-custom opc-sidebar opc-summary-wrapper custom-slide">
                      <OrderSummary
                        @contactDone="contactDone"
                        :checkPay="steps.checkPay"
                        :products="products"
                        :cart="cart"
                        :shippingMethod="shippingMethod"
                        @changeShippingMethod="changeShippingMethod"
                      ></OrderSummary>
                    </aside>
                  </div>
                </div>
              </div>
            </section>
          </div>
        </div>
        <div v-if="!isThankYou" class="checkout__aside desktop-only">
        </div>
      </div>
    </div>
    <div v-else>
      <nuxt-child />
    </div>
  </div>
</template>
<script>
  import {ref, computed, onMounted} from '@vue/composition-api';
  import {onSSR} from '@vue-storefront/core';
  import {useCart, cartGetters} from '@vue-storefront/magento';

  import {SfSteps, SfButton} from '@storefront-ui/vue';
  import OrderSummary from '~/components/checkout/OrderSummary';
  import MobileSummary from '~/components/checkout/MobileSummary';
  import ChooseStore from '~/components/checkout/ChooseStore';
  import ShopsJson from '@/content/ShopsList.json';

  const STEPS = {
    billing: 'Billing',
    shipping: 'Shipping',
    payment: 'Payment'
  };

  export default {
    layout: 'Checkout',
    name: 'Checkout',
    components: {
      SfButton,
      SfSteps,
      OrderSummary,
      MobileSummary,
      ChooseStore
    },

    setup(props, context) {
      const currentStep = computed(() => context.root.$route.path.split('/').pop());
      const currentStepIndex = computed(() => Object.keys(STEPS).findIndex(s => s === currentStep.value));
      const isThankYou = computed(() => currentStep.value === 'thank-you');

      const handleStepClick = (stepIndex) => {
        const key = Object.keys(STEPS)[stepIndex];
        context.root.$router.push(`/checkout/${key}`);
      };

      const products = computed(() => cartGetters.getItems(cart.value));

      let chooseStore = ref(false);
      let storeDetails = ref(false);
      let chosenStore = ref(true);
      let coords = [0, 0];
      let shippingStoreId = ref('4858835');

      const shops = ShopsJson;

      const shippingMethod = ref('pickup');
      const {cart, load} = useCart('cartPage');


      let shippingStore = computed(() => {
          let storeItem = shops.filter(
            (item) => item.id.toLowerCase() === shippingStoreId.value.toLowerCase()
          );
          coords = [storeItem[0].lat, storeItem[0].long];
          return storeItem;
        });

      if (currentStepIndex < 0) {
        handleStepClick(0);
      }

      onSSR(async () => {
        await load();
      });

      const contactDone = () => {
        handleStepClick(1);
      };
      const addressDone = () => {
        handleStepClick(2);
      };
      const changeShippingMethod = (val) => {
        shippingMethod.value = val;
      };
      const openChooseStore = (val) => {
        chooseStore.value = val;
      };
      const closeChooseStore = () => {
        chooseStore.value = false;
      };
      const openDetails = (value) => {
        shippingStoreId.value = value;
        chooseStore.value = false;

      };
      return {
        cartGetters,
        cart,
        products,
        handleStepClick,
        STEPS,
        currentStepIndex,
        isThankYou,
        currentStep,
        shops,
        zoom: 10,
        controls: [],
        coords,
        shippingStoreId,
        shippingStore,
        balloonType: '',
        steps: {
          contactData: true,
          delivery: false,
          checkPay: false
        },
        contactDone,
        addressDone,
        shippingMethod,
        changeShippingMethod,
        chooseStore,
        openChooseStore,
        closeChooseStore,
        storeDetails,
        chosenStore,
        openDetails
      };
    }
  };
</script>
