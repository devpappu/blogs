<template>
    <div>
        <div v-if="!paidFor">
            <h1>Buy this Lamp - ${{ product.price }} OBO</h1>

            <p>{{ product.description }}</p>

        </div>

        <div v-if="paidFor">
            <h1>Noice, you bought a beautiful lamp!</h1>
        </div>

        <div ref="paypal"></div>
    </div>
</template>


<script>
export default {
  name: "HelloWorld",

  data: function() {
    return {
      loaded: false,
      paidFor: false,
      product: {
        price: 777.77,
        description: "leg lamp from that one movie",
      }
    };
  },
  mounted: function() {
    const script = document.createElement("script");
    script.src =
      "https://www.paypal.com/sdk/js?client-id=AdGH9Y0lFtkfPAw2gFkQRa12l1YJKEiZsQq89pnZxPNw3H2I_Bu4Uw0uUomQU0xd4jZQiv8P5DkSAuE1";
    script.addEventListener("load", this.setLoaded);
    document.body.appendChild(script);
  },
  methods: {
    setLoaded: function() {
      this.loaded = true;
      window.paypal
        .Buttons({
          createOrder: (data, actions) => {
            return actions.order.create({
              purchase_units: [
                {
                  description: this.product.description,
                  amount: {
                    currency_code: "USD",
                    value: this.product.price
                  }
                }
              ]
            });
          },
          onApprove: async (data, actions) => {
            const order = await actions.order.capture();
            this.paidFor = true;
            console.log(order);
          },
          onError: err => {
            console.log(err);
          }
        })
        .render(this.$refs.paypal);
    }
  }
};
</script>
