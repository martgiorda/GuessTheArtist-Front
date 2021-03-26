<template>
  <transition name="modal">
        <div class="modal-mask">
          <div class="modal-wrapper">
            <div class="modal-container">

              <div class="modal-header">
              </div>

              <div class="modal-body">
                <h3 v-if="modalType === 'success'" style="color: #42b983;">
                    Well played !
                </h3>
                <h3 v-if="modalType === 'failure'" style="color: red;">
                    No :(
                </h3>
              </div>

              <div class="modal-footer">
                <slot name="footer">
                  
                  <button v-if="modalType === 'success'" class="modal-default-button btn btn-success" @click="$emit('close')">
                    Continue!
                  </button>
                  <button v-if="modalType === 'failure'" class="modal-default-button btn btn-danger" @click="$emit('close')">
                    Try Again!
                  </button>
                </slot>
              </div>
            </div>
          </div>
        </div>
      </transition>
</template>

<script>
export default {
  name: 'Modal',
  props: {
    modalType: String
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}

.modal-container {
  width: 300px;
  margin: 0px auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
  font-family: Helvetica, Arial, sans-serif;
}

.modal-body h3 {
  margin-top: 0;
  
}

.modal-body {
  margin: 20px 0;
}



/*
 * The following styles are auto-applied to elements with
 * transition="modal" when their visibility is toggled
 * by Vue.js.
 *
 * You can easily play with the modal transition by editing
 * these styles.
 */

.modal-enter {
  opacity: 0;
}

.modal-leave-active {
  opacity: 0;
}

.modal-enter .modal-container,
.modal-leave-active .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}

</style>
