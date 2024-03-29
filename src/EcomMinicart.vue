<template>
  <div class="ecom-minicart">
    <transition :duration="{ leave: 400 }">
      <div class="ecom-minicart__container" v-show="visible">
        <div
          class="ecom-minicart__overlay bg-dark fade"
          :class="{ 'show': fade }"
          v-if="overlay"
          @click="toggle">
        </div>

        <aside
          class="ecom-minicart__sidebar card shadow"
          :class="{ 'show': fade }">
          <slot name="header">
            <header class="card-header">
              {{ dictionary('my_cart') }}
              <button
                type="button"
                class="close"
                :aria-label="dictionary('close')"
                @click="toggle">
                <span aria-hidden="true">&times;</span>
              </button>
            </header>
          </slot>

          <main class="ecom-minicart__sidebar-body card-body">
            <transition-group
              name="ecom-minicart-list"
              tag="ul"
              class="ecom-minicart__list list-unstyled">
              <li
                class="ecom-minicart__item pb-3"
                v-for="item in cart.items"
                :key="item._id">

                <div class="ecom-minicart__item-name mb-3">
                  <a v-if="item.slug" :href="'/' + item.slug">
                    {{ item.name }}
                  </a>
                  <template v-else>
                    {{ item.name }}
                  </template>
                </div>

                <div class="d-flex align-items-center justify-content-between">
                  <a
                    v-if="item.picture && Object.keys(item.picture).length"
                    :href="item.slug ? '/' + item.slug : 'javascript:;'">
                    <img
                      :src="(item.picture.small || item.picture.normal ||
                        item.picture[Object.keys(item.picture)[0]]).url"
                      :alt="item.name"
                      class="ecom-minicart__item-picture" />
                  </a>

                  <input
                    type="number"
                    class="ml-2 ecom-minicart__item-quantity form-control form-control-sm"
                    v-model.number="item.quantity"
                    @change="handleItem(item)"/>
                  <div class="ecom-minicart__item-price flex-grow-1 text-center font-weight-bold">
                    {{ currencyValue(item.price * item.quantity, item.currency_symbol) }}
                  </div>
                  <div
                    class="ecom-minicart__item-remove text-right text-danger"
                    @click="removeItem(item._id)"
                    :aria-label="dictionary('remove')">
                    <i class="fas fa-trash-alt"></i>
                  </div>
                </div>
              </li>
            </transition-group>

            <div class="ecom-minicart__empty-cart">
              <slot name="empty">
                <p class="lead text-muted">
                  {{ dictionary('empty_cart') }} ...
                </p>
                <a
                  class="btn btn-block btn-primary"
                  href="javascript:;"
                  @click="toggle">
                  <span class="mr-1">
                    <i class="fas fa-arrow-left"></i>
                  </span>
                  {{ dictionary('continue_shopping') }}
                </a>
              </slot>
            </div>
          </main>

          <slot name="footer">
            <footer
              class="ecom-minicart__summary card-footer text-muted"
              v-if="cart.subtotal || cart.items.length">
              <div class="d-flex align-items-center justify-content-between pb-2">
                <span>Subtotal</span>
                <strong class="ecom-minicart__subtotal text-primary">
                  {{ currencyValue(cart.subtotal) }}
                </strong>
              </div>

              <a
                class="ecom-minicart__btn-checkout btn btn-block btn-primary"
                :href="checkoutUrl">
                <span class="mr-1">
                  <i class="fas fa-check"></i>
                </span>
                {{ dictionary('checkout') }}
              </a>
              <a
                class="ecom-minicart__btn-cart btn btn-block btn-outline-secondary"
                :href="cartUrl">
                {{ dictionary('see_cart') }}
              </a>
            </footer>
          </slot>
        </aside>
      </div>
    </transition>

    <slot name="button">
      <button
        type="button"
        class="ecom-minicart__button btn btn-lg btn-light"
        @click="toggle"
        :title="dictionary('open_cart')">
        <span class="ecom-minicart__button-icon">
          <i class="fas fa-shopping-bag"></i>
          <small class="ecom-minicart__button-badge st-block-secondary rounded font-weight-bold">
            {{ cart.items.length }}
          </small>
        </span>
        <small
          class="ecom-minicart__button-subtotal text-muted ml-1"
          v-if="buttonSubtotal">
          {{ currencyString(cart.subtotal) }}
        </small>
      </button>
    </slot>
  </div>
</template>

<script src="./EcomMinicart.js"></script>
<style lang="scss" src="./EcomMinicart.scss"></style>
