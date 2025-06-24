<template>
  <div class="card-item" :class="{ '-active': isCardFlipped }" v-if="labels && inputFields">
    <div class="card-item__side -front">
      <div :class="{ '-active': focusElementStyle }" :style="focusElementStyle" class="card-item__focus"
        ref="focusElement"></div>
      <div class="card-item__cover" :aria-label="imageCover">
        <img v-if="currentCardBackground" :src="currentCardBackground" class="card-item__bg" alt="Background image" />
      </div>
      <div class="card-item__wrapper">
        <div class="card-item__top">
          <img src="../assets/images/chip.png" class="card-item__chip" alt="Card chip image" />
          <div class="card-item__type">
            <transition name="slide-fade-up">
              <img v-if="cardType" :src="getCreditCardImage" :key="cardType" :alt="`${cardType} brand image`"
                class="card-item__typeImg" />
            </transition>
          </div>
        </div>
        <label :for="inputFields.cardNumber" :ref="inputFields.cardNumber" aria-label="Card number"
          class="card-item__number">
          <span v-for="(n, $index) in currentPlaceholder" :key="$index">
            <transition name="slide-fade-up">
              <div v-if="getIsNumberMasked($index, n)" class="card-item__numberItem">
                *
              </div>
              <div v-else-if="valueFields.cardNumber.length > $index" :class="{ '-active': n.trim() === '' }"
                :key="currentPlaceholder" class="card-item__numberItem">
                {{ valueFields.cardNumber[$index] }}
              </div>
              <div v-else :class="{ '-active': n.trim() === '' }" :key="currentPlaceholder + 1"
                class="card-item__numberItem">
                {{ n }}
              </div>
            </transition>
          </span>
        </label>
        <div class="card-item__content">
          <label :for="inputFields.cardName" :ref="inputFields.cardName" aria-label="Card name" class="card-item__info">
            <div class="card-item__holder">
              {{ labels.cardHolder || "Card Holder" }}
            </div>
            <transition name="slide-fade-up">
              <div v-if="valueFields.cardName.length" class="card-item__name" key="1">
                <transition-group name="slide-fade-right">
                  <span v-for="(n, key) in valueFields.cardName.replace(
                    /\s\s+/g,
                    ' '
                  )" :key="key + 1" class="card-item__nameItem">{{ n }}</span>
                </transition-group>
              </div>
              <div class="card-item__name" v-else key="2">
                {{ labels.cardName || "Full Name" }}
              </div>
            </transition>
          </label>
          <div class="card-item__date" ref="cardDate">
            <label :for="inputFields.cardMonth" class="card-item__dateTitle" aria-label="Expiration date">{{
              labels.cardExpires || "Expires" }}</label>
            <label :for="inputFields.cardMonth" class="card-item__dateItem" aria-label="Card month">
              <transition name="slide-fade-up">
                <span v-if="valueFields.cardMonth" :key="valueFields.cardMonth">{{ valueFields.cardMonth }}</span>
                <span v-else key="2">{{ labels.cardMonth || "MM" }}</span>
              </transition>
            </label>
            /
            <label :for="inputFields.cardYear" class="card-item__dateItem" aria-label="Card year">
              <transition name="slide-fade-up">
                <span v-if="valueFields.cardYear" :key="valueFields.cardYear">{{
                  String(valueFields.cardYear)
                }}</span>
                <span v-else key="2">{{ labels.cardYear || "YY" }}</span>
              </transition>
            </label>
          </div>
        </div>
      </div>
    </div>
    <div class="card-item__side -back">
      <div class="card-item__cover" :aria-label="imageCover">
        <img v-if="currentCardBackground" :src="currentCardBackground" class="card-item__bg" alt="Background image" />
      </div>
      <div class="card-item__band"></div>
      <div class="card-item__cvv">
        <label :for="inputFields.cardCvv" aria-label="Card CVV">
          <div class="card-item__cvvTitle">{{ labels.cardCvv }}</div>
          <div class="card-item__cvvBand">
            <span>{{ valueFields.cardCvv }}</span>
          </div>
        </label>
        <div class="card-item__type">
          <img v-if="cardType" :src="getCreditCardImage" class="card-item__typeImg" alt="Dark bar image" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'VuePaycard',
  props: {
    valueFields: {
      type: Object,
      required: true
    },
    inputFields: {
      type: Object,
      default: () => ({
        cardNumber: 'v-card-number',
        cardName: 'v-card-name',
        cardMonth: 'v-card-month',
        cardYear: 'v-card-year',
        cardCvv: 'v-card-cvv'
      })
    },
    labels: {
      type: Object,
      default: () => ({
        cardName: 'Full Name',
        cardHolder: 'Card Holder',
        cardMonth: 'MM',
        cardYear: 'YY',
        cardExpires: 'Expires',
        cardCvv: 'CVV'
      })
    },
    isCardNumberMasked: {
      type: Boolean,
      default: true
    },
    hasRandomBackgrounds: {
      type: Boolean,
      default: true
    },
    backgroundImage: {
      type: [String, Number],
      default: ''
    },
    setType: {
      type: String,
      default: ''
    },
    isCardFlipped: {
      type: Boolean,
      default: false
    }
  },
  emits: ['get-type'],
  data () {
    const defaultPlaceholder = '#### #### #### ####'

    return {
      focusElementStyle: null,
      currentFocus: null,
      isFocused: false,
      amexCardPlaceholder: '#### ###### #####',
      fifteenCardPlaceholder: '#### #### #### ###',
      dinersCardPlaceholder: '#### ###### ####',
      unionPayCardPlaceholder: '###### ####### ######',
      defaultCardPlaceholder: defaultPlaceholder,
      currentPlaceholder: defaultPlaceholder
    }
  },
  watch: {
    currentFocus () {
      if (this.currentFocus) {
        this.changeFocus()
      } else {
        this.focusElementStyle = null
      }
    },
    cardType (val) {
      this.$emit('get-type', val)
      this.changePlaceholder()
    }
  },
  mounted () {
    this.init()
  },
  beforeDestroy () {
    this.destroy()
  },
  // * This can't be tested since the project runs in Vue 2
  /* istanbul ignore next */
  beforeUnmount () {
    /* istanbul ignore next */
    this.destroy()
  },
  computed: {
    jcbCardPlaceholder () {
      const number = this.valueFields.cardNumber.replace(/\s+/g, '')

      return number.startsWith('2131') || number.startsWith('1800')
        ? this.fifteenCardPlaceholder
        : this.defaultPlaceholder
    },
    getCreditCardImage () {
      const path = new URL(`../assets/images/${this.cardType}.png`, import.meta.url).href
      return path.default || path
    },
    cardType () {
      const acceptedTypes = [
        'visa',
        'mastercard'
      ]
      const setType = this.setType?.toLowerCase()?.replace(/ /g, '')
      if (setType?.length && acceptedTypes.includes(setType)) return setType

      const number = this.valueFields.cardNumber.replace(/\s+/g, '')

      if (number.match(/^4\d{12}(\d{3})?$/)) return 'visa'

      if (number.match(/^(5[1-5]\d{4}|677189)\d{10}$/)) return 'mastercard'

      return ''
    },
    imageCover () {
      return !this.hasRandomBackgrounds && parseInt(this.backgroundImage)
        ? 'Image cover'
        : ''
    },
    isBackgroundImageFromAssets () {
      const numberImage = parseInt(this.backgroundImage)

      return (
        Number.isFinite(numberImage) &&
        parseInt(numberImage) < 26 &&
        parseInt(numberImage) > 0
      )
    },
    currentCardBackground () {
      const numberImage = parseInt(this.backgroundImage)

      if (this.isBackgroundImageFromAssets) {
        const path = new URL(`../assets/images/${numberImage}.jpg`, import.meta.url).href
        return path.default || path
      }

      if (this.backgroundImage && !Number.isFinite(numberImage)) {
        return this.backgroundImage
      }

      if (this.hasRandomBackgrounds) {
        const random = Math.floor(Math.random() * 25 + 1)

        const path = new URL(`../assets/images/${random}.jpg`, import.meta.url).href
        return path.default || path
      }

      return null
    }
  },
  methods: {
    addOrRemoveFieldListeners (event = 'addEventListener') {
      const self = this
      const fields = document.querySelectorAll('[data-card-field]')
      fields.forEach((element) => {
        element[event]('focus', () => {
          this.isFocused = true
          if (
            element.id === this.inputFields.cardYear ||
            element.id === this.inputFields.cardMonth
          ) {
            this.currentFocus = 'cardDate'
          } else {
            this.currentFocus = element.id
          }
          // this.isCardFlipped = element.id === this.inputFields.cardCvv
        })
        element[event]('blur', () => {
          // this.isCardFlipped = !element.id === this.inputFields.cardCvv
          const timeout = setTimeout(() => {
            if (!self.isFocused) {
              self.currentFocus = null
            }
            clearTimeout(timeout)
          }, 300)
          self.isFocused = false
        })
      })
    },
    init () {
      this.addOrRemoveFieldListeners()
    },
    destroy () {
      this.addOrRemoveFieldListeners('removeEventListener')
    },
    changeFocus () {
      const target = this.$refs[this.currentFocus]
      this.focusElementStyle = target
        ? {
            width: `${target.offsetWidth}px`,
            height: `${target.offsetHeight}px`,
            transform: `translateX(${target.offsetLeft}px) translateY(${target.offsetTop}px)`
          }
        : null
    },
    getIsNumberMasked (index, n) {
      const numbers = this.cardType === 'amex' ? 13 : 14

      return (
        index < numbers &&
        this.valueFields.cardNumber.length > index &&
        n.trim() !== '' &&
        this.isCardNumberMasked
      )
    },
    changePlaceholder () {
      const cardsPlaceholder = {
        amex: this.amexCardPlaceholder,
        dinersclub: this.dinersCardPlaceholder,
        jcb: this.jcbCardPlaceholder,
        uatp: this.fifteenCardPlaceholder,
        unionpay: this.unionPayCardPlaceholder
      }

      this.currentPlaceholder =
        cardsPlaceholder[this.cardType] || this.defaultCardPlaceholder
      this.$nextTick(() => {
        this.changeFocus()
      })
    }
  }
}
</script>

<style src="../assets/css/style.min.css" scoped></style>
