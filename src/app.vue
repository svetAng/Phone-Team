

<template >
  <!-- App -->
  <div id="app">

    <!-- Statusbar -->
    <f7-statusbar></f7-statusbar>

    <!-- Left Panel -->
    <f7-panel left layout="dark">
      <f7-view id="left-panel-view" navbar-through :dynamic-navbar="true">
        <f7-pages>
          <f7-page >
            <f7-navbar v-if="$theme.material" title="Options" sliding></f7-navbar>
            <f7-list @test="test" class="theme-red" form>
              <f7-list-item  link="/days/" title="Days Open"></f7-list-item>

              <f7-list-item checkbox name="toggleWhisper" title="Whisper">
              </f7-list-item>

              <f7-list-item  @change="voicemailActive = !voicemailActive" checkbox name="togglevoicemail" title="Voicemail">
              </f7-list-item>



              <f7-list-item v-if="voicemailActive">
                <f7-label style="width: 55px;">
                  <f7-icon f7="email"></f7-icon>
                </f7-label>
                <f7-input type="email" icon-f7="volume" placeholder="Email for voicemail"/>
              </f7-list-item>


              <f7-list-item checkbox name="my-checkbox" v-model="welcome" title="Welcome Message">
              </f7-list-item>

              <f7-buttons v-if="welcome">
                <f7-button icon-f7="mic" color="white">
                  <span @click="openRecorder" style="padding-left: 10px;">
                    Record
                  </span>
                </f7-button>
                <f7-button @click="playRecording" :disabled="!recordingExists" icon-f7="play" color="white">
                  <span style="padding-left: 10px;">
                    Play
                  </span>
                </f7-button>
              </f7-buttons>
            </f7-list>

            <f7-list>

              <f7-list-item link="/profile/" title="Profile" link-view="#main-view" link-close-panel></f7-list-item>
              <f7-list-item>
                <f7-label>
                  <f7-icon  class="timeColor" f7="login" style="width:40px;"></f7-icon>
                    <div class="timeColor"> Opening Time
                    </div>
                </f7-label>
                <f7-input type="text" v-modal="openTime" v-on:blur='changeOpenTime()' readonly id="picker-open-time"></f7-input>
              </f7-list-item>
              <f7-list-item>
                <f7-label>
                  <f7-icon class="timeColor" f7="logout" style="width:40px;"></f7-icon>
                  <div class="timeColor"> Closing Time
                  </div>

                </f7-label>
                <f7-input type="text" v-model="closingTime" v-on:blur='changeClosingTime()' readonly id="picker-closing-time"></f7-input>
              </f7-list-item>
            </f7-list>
          </f7-page>
        </f7-pages>
      </f7-view>
    </f7-panel>


    <!-- Main Views -->
    <f7-views>
      <f7-view id="main-view" navbar-through :dynamic-navbar="true" main>
        <!-- Pages -->
        <f7-pages>
          <f7-page>
            <!-- Material Theme Navbar -->
            <f7-navbar v-if="$theme.material">
              <f7-nav-left>
                <f7-link icon="icon-bars" open-panel="left"></f7-link>

              </f7-nav-left>
              <f7-nav-center sliding>PhoneTeam</f7-nav-center>
              <f7-nav-right>
                <!-- Link to enable/disable sortable -->
                <f7-link toggle-sortable="#sortable">
                  <div v-html="getSortingIcon()"></div>

                </f7-link>
              </f7-nav-right>
            </f7-navbar>
            <!-- Page Content -->
            <f7-block-title>
              Agents
            </f7-block-title>


            <f7-list
            media-list
            id="sortable"
            sortable
            @sortable:sort="onSort"
            @sortable:open="onOpen"
            @sortable:close="onClose">

            <f7-list-item
            v-for="agent in agents"
            :key="agent.id"
            :media="getIcon(agent.id)"
            @swipeout:open="open"
            @click="editAgent(agent)"
            swipeout
            :title="agent.name"
            :subtitle="agent.number">

            <f7-swipeout-actions left>
              <f7-swipeout-button close :id="getActivateId(agent.id)" color="green">
                <f7-icon f7="play_fill" size="22"></f7-icon>
              </f7-swipeout-button>
            </f7-swipeout-actions>
            <f7-swipeout-actions right>
              <f7-swipeout-button close :id="getDeactivateId(agent.id)" color="red">
                <f7-icon f7="pause_fill" size="22"></f7-icon>
              </f7-swipeout-button>
            </f7-swipeout-actions>
          </f7-list-item>
        </f7-list>


        <f7-fab @click="addAgent = true" color="red">
          <f7-icon icon="icon-plus"></f7-icon>
        </f7-fab>

      </f7-page>
    </f7-pages>
  </f7-view>
</f7-views>

<!-- Popup Add Agent -->
<f7-popup @popup:closed="addAgent = false" :opened="addAgent" id="popup">
  <f7-view navbar-fixed>
    <f7-pages>
      <f7-page>
        <f7-navbar title="Add Agent">
          <f7-nav-right>
            <f7-link close-popup>
              <f7-icon f7="close"></f7-icon>
            </f7-link>
          </f7-nav-right>
        </f7-navbar>
        <f7-block-title>Agent Details</f7-block-title>
        <f7-list form>
          <f7-list-item>
            <f7-icon slot="media" f7="person"></f7-icon>
            <f7-label floating>Name</f7-label>
            <f7-input v-model="newAgentName" type="text" placeholder="Name"/>
          </f7-list-item>
          <f7-list-item>
            <f7-icon slot="media" f7="phone_round_fill"></f7-icon>
            <f7-label floating>Number</f7-label>
            <f7-input v-model="newAgentNumber" type="number" placeholder="31701234567"/>
          </f7-list-item>
        </f7-list>

        <f7-fab @click="saveAgent" color="red">
          <div class="col-25">
    Default<br>
    <span class="preloader"></span>
  </div>
          <f7-icon f7="check"></f7-icon>
        </f7-fab>
      </f7-page>
    </f7-pages>
  </f7-view>
</f7-popup>

<!-- Popup Edit Agent -->
<f7-popup @popup:closed="editAgentOpen = false" :opened="editAgentOpen" id="popup">
  <f7-view navbar-fixed>
    <f7-pages>
      <f7-page>
        <f7-navbar title="Edit Agent">
          <f7-nav-right>
            <f7-link @click="deleteAgent">
              <f7-icon f7="trash"></f7-icon>
            </f7-link>
            <f7-link @click="editAgentOpen = false">
              <f7-icon f7="close"></f7-icon>
            </f7-link>
          </f7-nav-right>
        </f7-navbar>
        <f7-block-title>Agent Details</f7-block-title>
        <f7-list form>
          <f7-list-item>
            <f7-icon slot="media" f7="person"></f7-icon>
            <f7-label>Name</f7-label>
            <f7-input v-model="activeAgent.name" type="text"/>
          </f7-list-item>
          <f7-list-item>
            <f7-icon slot="media" f7="phone_round_fill"></f7-icon>
            <f7-label>Number</f7-label>
            <f7-input v-model="activeAgent.number" type="number"/>
          </f7-list-item>
        </f7-list>
        <f7-fab @click="saveEditAgent" color="red" >
          <f7-icon f7="check"></f7-icon>
        </f7-fab>
      </f7-page>
    </f7-pages>
  </f7-view>
</f7-popup>


</div>
</template>

<script>
import jquery from 'jquery';
import Vue from 'vue';

import Toast from './plugin.js'
import ToastStyles from './plugin.css'

let $ = jquery;

export default {
  data() {
    return {
      sorting: false,
      agents: [],
      activeAgent: '',
      addAgent: false,
      editAgentOpen: false,
      newAgentName: '',
      newAgentNumber: '',
      file: '',
      filePath: '',
      recordingExists: false,
      voicemailActive: false,
      openTime: '09:00',
      closingTime: '17:00',
      welcome: false,

    }
  },
  methods: {
    test() {
        console.log('tesst'
      )
    },
    onF7Init() {

      // Create the picker for time
      window.f7.picker({
        input: '#picker-open-time',
        rotateEffect: true,
        cols: [
          // Hours
          {
            values: (function () {
              var arr = [];
              for (var i = 0; i <= 23; i++) { arr.push(i < 10 ? '0' + i : i); }
              return arr;
            })()
          },
          // Divider
          {
            divider: true,
            content: ':'
          },
          // Minutes
          {
            values: (function () {
              var arr = [];
              for (var i = 0; i <= 59; i++) { arr.push(i < 10 ? '0' + i : i); }
              return arr;
            })(),
          }
        ]
      });

      window.f7.picker({
        input: '#picker-closing-time',
        rotateEffect: true,
        cols: [
          // Hours
          {
            values: (function () {
              var arr = [];
              for (var i = 0; i <= 23; i++) { arr.push(i < 10 ? '0' + i : i); }
              return arr;
            })()
          },
          // Divider
          {
            divider: true,
            content: ':',


          },
          // Minutes
          {
            values: (function () {
              var arr = [];
              for (var i = 0; i <= 59; i++) { arr.push(i < 10 ? '0' + i : i); }
              return arr;
            })(),
          }
        ]
      });
    },
    changeOpenTime() {

      let time = $('#picker-open-time').val();
      this.openTime = time.replace(/ /g,':');

    },
    changeClosingTime() {
      let time = $('#picker-closing-time').val();
      this.closingTime = time.replace(/ /g,':');
    },
    deleteAgent() {
      this.agents.splice(this.activeAgent.id, 1);
      this.editAgentOpen = false;

      let vm = this;
      this.agents.forEach(function (value, i) {
        vm.agents[i].id = i;
      });
    },
    editAgent(agent) {
      this.activeAgent = agent;
      this.editAgentOpen = true;
    },
    saveEditAgent() {
      this.agents[this.activeAgent.id].name = this.activeAgent.name;
      this.agents[this.activeAgent.id].number = this.activeAgent.number;

      this.editAgentOpen = false;
    },
    saveAgent() {
      let agent = new Object;
      agent.name = this.newAgentName;
      agent.number = this.newAgentNumber;
      agent.id = this.agents.length;
      this.agents.push(agent);

      this.newAgentName = '';
      this.newAgentNumber = '';
      this.addAgent = false;
    },
    getSortingIcon() {
      if (this.sorting) {
        return '<i class="icon f7-icons">check</i>';
      } else {
        return '<i class="icon f7-icons">sort</i>';
      }
    },
    getIcon(id) {
      return "<i id='status-icon-" + id + "' style='font-size:30px' class='icon color-red fa fa-circle'></i>"
    },
    getActivateId(id) {
      return 'activate-' + id;
    },
    getDeactivateId(id) {
      return 'deactivate-' + id;
    },
    getColor(id) {
      if (!this.agents[id].status) {
        return 'green';
      } else {
        return 'red';
      }
    },
    open(params) {

      let vm = this;



      Vue.nextTick(function () {
        let idOfToggle = $('.swipeout-actions-opened > a').attr('id');

        let id = idOfToggle.substring(idOfToggle.indexOf("-") + 1)
        let action = idOfToggle.substring(0, idOfToggle.indexOf("-"));

        console.log('id: ' + id);
        console.log('action: ' + action);

        window.f7.swipeoutClose(window.f7.swipeoutOpenedEl);

        let agent = vm.agents[id];

        if (action === 'activate') {

          window.f7.toast('&nbsp', '<i style="font-size: 80px" class="icon f7-icons size-50">play_fill</i>', { duration: 400 }).show(true);


          $('#status-icon-' + agent.id).removeClass('color-red');
          $('#status-icon-' + agent.id).addClass('color-green');
        } else {

          window.f7.toast('&nbsp', '<i style="font-size: 80px" class="icon f7-icons size-50">pause_fill</i>', { duration: 400 }).show(true);

          $('#status-icon-' + agent.id).addClass('color-red');
          $('#status-icon-' + agent.id).removeClass('color-green');
        }


      });
    },
    doSomething() {
      $('#disable-1').parent('div').addClass('swipeout-actions-opened');
    },
    onOpen: function () {
      this.sorting = !this.sorting;
    },
    onClose: function () {
      this.sorting = !this.sorting;
    },
    onSort: function (event, indexes) {
      console.log('sort', indexes);
    },



    // Methods for capturing audio
    openRecorder() {
      console.log(navigator);
      navigator.device.capture.captureAudio(this.captureSuccess, this.captureError);
    },

    captureError(error) {
      navigator.notification.alert('Error code: ' + error.code, null, 'Capture Error');
    },

    captureSuccess(mediaFiles) {
      this.file = mediaFiles[0].localURL;
      this.filePath = mediaFiles[0].fullPath;

      this.recordingExists = true;
      this.$forceUpdate();
    },

    playRecording() {
      this.media = new Media(this.file, function(e) {
        media.release();
      }, function(err) {
      });

      this.media.play();
    },
  },
  mounted() {


    this.agents = [
      {id: 0, name: "Pieter Overbeek", number: "31702211463", status: false },
      {id: 1, name: "Svetoslav Angelov", number: "31854013961", status: false },
      {id: 2, name: "Rene Beekman", number: "31703111050", status: false }
    ];

    console.log(this.agents);
  }
}
</script>
<style media="screen">
.timeColor {
  color: white;
}

</style>
