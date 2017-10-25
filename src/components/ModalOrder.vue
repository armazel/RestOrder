<template>
    <ui-modal class="modal-good-group" ref="modal" dismissOn="esc close-button" @open="visible = true" @close="visible = false">
        <template slot="header">
            <h2> <ui-icon class="ui-modal__header-icon" icon="build" style="margin-right: 10px"></ui-icon>Изменение заказа № {{numberOrder}}</h2>
        </template>

        <template>
          <div class="option">
            <div class="option-block">

              <ui-textbox
                floating-label
                icon="vpn_key"
                label="Номер заказа"
                v-model="numberOrder">
              </ui-textbox>

              <ui-datepicker
                floating-label
                orientation="landscape"
                picker-type="modal"
                icon="events"
                placeholder="Select a date"
                v-model="date"
                :lang="langMass"
              >Дата/время создания
              </ui-datepicker>

              <ui-select
                class="configs"
                placeholder="Точка доставки"
                label="Точка доставки"
                icon="edit_location"
                v-model="pointOfDelivery"
                :options="pointOfDeliverySend"
                :keys="{label: 'name', value: 'select'}">
              </ui-select>

              <ui-textbox
                floating-label
                icon="person"
                label="ФИО"
                v-model="clientInfo">
              </ui-textbox>

              <ui-textbox
                floating-label
                icon="edit_location"
                label="Адресс"
                v-model="addressInfo">
              </ui-textbox>

            </div>

            <div class="option-block">

              <ui-textbox
                floating-label
                icon="call"
                label="Номер телефона"
                v-model="phoneNumber">
              </ui-textbox>

              <ui-select floating-label
                         label="Тип доставки"
                         icon="send"
                         class="configs"
                         placeholder="Статус"
                         v-model="type"
                         :options="typeDelivery"
                         :keys="{label: 'name', value: 'select'}">
              </ui-select>

              <ui-select floating-label
                         label="Статус"
                         icon="info"
                         class="configs"
                         placeholder="Статус"
                         v-model="statusOrder"
                         :options="typeStatus"
                         :keys="{label: 'name', value: 'select'}">
              </ui-select>

              <ui-datepicker
                floating-label
                orientation="landscape"
                picker-type="modal"
                icon="events"
                placeholder="Select a date"
                v-model="timeSend"
                :lang="langMass"
              >Дата/время доставки
              </ui-datepicker>

              <ui-textbox
                floating-label
                icon="supervisor_account"
                label="Имя курьера"
                v-model="curierName">
              </ui-textbox>

            </div>
          </div>
        </template>

      <template slot="footer">
        <ui-button type="primary" color="primary" icon="done" @click="save">Сохранить</ui-button>
        <ui-button @click="close">Закрыть</ui-button>
      </template>
    </ui-modal>
</template>

<script>
    import Api from '../api'
    import {actions} from '../store'
    import {moments} from '../utils'
    import {OrderDocument} from '../data/order'
    import languageFr from '../utils/lang/lang-ru'
    import Order from '../data/order'

    export default {
        name: 'modal-order',

        components: {

        },

        data() {
            return {
                langMass:languageFr,
                visible: false,
                saving: false,

                id: -1,
                groupKey: -1,
                name: '',
                alias: '',
                model: '',
                valueKey: -1,
                groupName:'',

                selectedValue: 0,

              numberOrder: '',
              date: new Date(),
              pointOfDelivery: '',
              clientInfo: '',
              addressInfo: '',
              phoneNumber: '',
              statusOrder: '',
              timeSend: new Date(),
              type: '',
              curierName: '',

              pointOfDeliverySend: [
                {
                  name: 'нет',
                  select: false
                },
                {
                  name: 'Карлова 8',
                  select: true
                },
                {
                  name: 'Азазаза 14',
                  select: true
                },
                {
                  name: 'Фрунзе 34',
                  select: true
                }
              ],

              typeStatus:[
                {
                  name: 'нет',
                  select: false
                },
                {
                  name: 'доставка',
                  select: true
                },
                {
                  name: 'готовка',
                  select: true
                },
                {
                  name: 'апокалипсис сегодня',
                  select: true
                }
              ],

              typeDelivery:[
                {
                  name: 'нет',
                  select: false
                },
                {
                  name: 'курьер',
                  select: true
                },
                {
                  name: 'самовывоз',
                  select: true
                }
              ],
            };
        },

        computed: {

            values() {
                return this.$store.getters.values;
            }
        },

        watch: {
            saving(value) {
                this.$refs['modal'].dismissable = !value;
            },

           /* selectedValue(value) {
                this.valueKey = value ? value.id : -1;
            }*/
        },

        methods: {
          open(order) {
            // Данные диалога (редактирование существующего товара или создание нового)
            if (order) {
              this.numberOrder = order.id
              this.date = order.date
              this.pointOfDelivery = order.pointOfDelivery
              this.clientInfo = order.client
              this.addressInfo = order.addressInfo
              this.phoneNumber = order.phoneNumber
              this.statusOrder = order.statusOrder
              this.timeSend = order.timeSend
              this.type = order.type
              this.curierName = order.curierName
            } else {
              this.numberOrder = ''
              this.date = ''
              this.pointOfDelivery = ''
              this.client = ''
              this.addressInfo = ''
              this.phoneNumber = ''
              this.statusOrder = ''
              this.timeSend = ''
              this.type = ''
              this.curierName = ''
            }

            // Обновим список единиц измерения в vuex

            this.$refs['modal'].open();
          },

            /*getGoodGroup(id){
                this.$store.dispatch(actions.getGoodGroup,id);
            },*/

            close() {
                if (this.saving) {
                    return;
                }

                this.$refs['modal'].close();
            },

            save() {

                /*// Проверяем обязательные поля
                if (!(this.groupKey > 0)) {
                    this.$store.dispatch(actions.addAlertWarning, 'Не выбрана товарная группа');
                    return;
                }
                if (!(this.valueKey > 0)) {
                    this.$store.dispatch(actions.addAlertWarning, 'Не выбрана единица измерения');
                    return;
                }*/

                // Данные которые отправятся на сервер
                const order = Order.create();
                if (this.numberOrder > 0) {
                  order.id = this.numberOrder;
                }
               order.date = this.date
               order.pointOfDelivery = this.pointOfDelivery
               order.client = this.clientInfo
               order.addressInfo = this.addressInfo
               order.phoneNumber = this.phoneNumber
               order.statusOrder = this.statusOrder
               order.timeSend = this.timeSend
               order.type = this.type
               order.curierName = this.curierName

                // Сохраняем объект на сервере
                this.$store.dispatch(actions.saveOrder, good)
                    .then(() => {
                        this.saving = false;
                        this.$store.dispatch(actions.addAlertSuccess, 'Заказ сохранен');
                        this.close();
                        /*Перезагрузка страницы*/
                        this.$store.dispatch(actions.reQueryGoods);
                    })
                    .catch((error) => {
                        this.saving = false;
                        this.$store.dispatch(actions.addAlertError, 'Ошибка сохранения товара: ' + (error && error.message || error));
                    });
            }
        }
    };
</script>

<style lang="scss">
  .option{
    display: flex;
    margin: 10px 10px;
    .option-block{
      width: 48%;
      .ui-textbox.has-floating-label .ui-textbox__label-text.is-inline{
        font-size: 14px;
        font-family: "Helvetica Neue", Roboto, Arial, "Droid Sans", sans-serif;
      }
      .ui-select{
        width: 100%;
        text-align: left;
      }
    }
  }
  .option-block:not(:first-child){
    margin-left: 20px;
  }
  .ui-modal__footer{
    height: 3.5rem;
    box-shadow: 0 -1px 1px rgba(0, 0, 0, 0.16);
    background-color: #f5f5f5;
  }
  .ui-datepicker__display-value{
    text-align: left !important;
  }
  .ui-modal__container{
    width: 49rem;
  }
</style>
