<template>
  <div class="right-side" id="sidebar">
    <no-ssr>
      <div class="nav">
        <div class="stats" v-bind:class="{pointer: chatCursor}" @click="toChat()">
          <span class="chat-text">CHAT</span>
          <div class="viewers">
            <svg class="viewers-icon" style="width:16px; height:12px;enable-background:new 0 0 555 424.3;" version="1.1"
                 id="Capa_1"
                 xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px"
                 y="0px" viewBox="0 0 555 424.3"
                 xml:space="preserve" fill="#F6CE4F">
            <g>
              <g>
                <path d="M481.1,254.5h-39.6c4,11,6.2,23,6.2,35.4v119.6c0,5.2-0.9,10.2-2.5,14.8h65.5c24.5,0,44.3-19.9,44.3-44.3v-51.6
                                                                			C555,287.7,521.8,254.5,481.1,254.5z"/>
              </g>
            </g>
              <g>
              <g>
                <path d="M107.3,289.9c0-12.4,2.2-24.4,6.2-35.4H73.9C33.1,254.5,0,287.7,0,328.4V380c0,24.5,19.9,44.3,44.3,44.3h65.5
                                                                			c-1.6-4.6-2.5-9.6-2.5-14.8V289.9z"/>
              </g>
            </g>
              <g>
              <g>
                <path d="M323.3,216h-90.5c-40.8,0-73.9,33.2-73.9,73.9v119.6c0,8.2,6.6,14.8,14.8,14.8h208.8c8.2,0,14.8-6.6,14.8-14.8V289.9
                                                                			C397.2,249.2,364,216,323.3,216z"/>
              </g>
            </g>
              <g>
              <g>
                <path d="M278,0c-49,0-88.9,39.9-88.9,88.9c0,33.2,18.3,62.3,45.4,77.5c12.9,7.2,27.7,11.4,43.4,11.4s30.6-4.1,43.4-11.4
                                                                			c27.1-15.2,45.4-44.3,45.4-77.5C366.9,39.9,327,0,278,0z"/>
              </g>
            </g>
              <g>
              <g>
                <path d="M99.9,82.9c-36.7,0-66.5,29.8-66.5,66.5s29.8,66.5,66.5,66.5c9.3,0,18.2-1.9,26.2-5.4c13.9-6,25.4-16.6,32.5-29.9
                                                                			c5-9.3,7.8-19.9,7.8-31.2C166.4,112.7,136.6,82.9,99.9,82.9z"/>
              </g>
            </g>
              <g>
              <g>
                <path d="M457.1,82.9c-36.7,0-66.5,29.8-66.5,66.5c0,11.3,2.8,21.9,7.8,31.2c7.1,13.3,18.6,23.9,32.5,29.9c8,3.5,16.9,5.4,26.2,5.4
                                                                			c36.7,0,66.5-29.8,66.5-66.5S493.7,82.9,457.1,82.9z"/>
              </g>
            </g>
          </svg>
            <span class="count">{{ viewers_count }}</span>
            <div class="message-unread" v-bind:class="{big: unreadMessages > 99}"
                 v-if="!isNaN(unreadMessages) && unreadMessages > 0">
              {{ (unreadMessages > 99) ? '99+' : unreadMessages }}
            </div>
          </div>
        </div>
        <div class="links">
          <span v-on:click="showAbout()">ABOUT</span>
          <span v-on:click="showFAQ()" v-bind:class="{ show: faqLink }" class="faq_nav">FAQ</span>
        </div>
      </div>

      <about v-if="webinar.blocks" :webinar="webinar"/>

      <FAQ v-if="webinar.blocks" :faq="webinar.blocks.faq" ref="faq"/>

      <chat :webinar="webinar" :bannerStatus="bannerStatus" :user="user" :sortMessages="sortMessages" :webinar_name="webinar_name"/>

      <div class="message">
        <div class="ad" v-bind:class="{ show: bannerStatus }" v-if="webinar.chat && webinar.chat.banner">
          <div class="ad-offer">
            <img src="~assets/img/minimize.png" class="minimize-btn" v-on:click="toggleAd()" alt="Image">
            <img src="~assets/img/minimize-mobile.png" class="minimize-btn-mobile" v-on:click="toggleAd()" alt="Image">
            <img v-if="webinar.chat.banner.image" :src="webinar.chat.banner.image" class="ad-picture ad-picture--usual" alt="Image">
          </div>
          <div class="ad-price">
            <span class="special">SPECIAL WEBINAR OFFER</span>
            <span class="ad-discount">JUST ${{ webinar.chat.banner.special_price}}</span>
          </div>
          <div class="ad-bottom">
            <div class="countdown">
              <p class="expiration">OFFER EXPIRES IN</p>
              <p class="timer">{{ banner_time }}</p>
              <div class="measurements">
              <span class="hrs">
                HRS
              </span>
                <span class="mins">
                MINS
              </span>
                <span class="secs">
                SECS
              </span>
              </div>
            </div>
            <a :href="redirect_url" target="_blank" class="enroll-btn">{{ webinar.chat.banner.button.text}}</a>
            <div class="packages-remaining">
              <span class="packages-count">{{ packages }}</span>
              <span v-html="webinar.chat.banner.big_title" v-if="packages > 1"></span>
              <span v-html="webinar.chat.banner.big_title_single" v-else></span>
            </div>
          </div>
        </div>
        <div class="ad-minimized" v-bind:class="{ show: minimizeBanner }" v-if="webinar.chat && webinar.chat.banner"
             @click="toggleAd()">
          <div class="packages-remaining">
          <span>
            <span class='packages-count'>{{ packages }}</span>
            <span v-html="webinar.chat.banner.title"></span>
          </span>
          </div>
          <div class="countdown">
            <p class="timer" id="banner_timer">{{ banner_time }}</p>
            <div v-if="banner_time != 'EXPIRED'" class="measurements">
            <span class="hrs">
              HRS
            </span>
              <span class="mins">
              MINS
            </span>
              <span class="secs">
              SECS
            </span>
            </div>
          </div>
          <a :href="redirect_url" @click="toggleAd()" target="_blank" class="enroll-btn">{{webinar.chat.banner.button.text}}</a>
        </div>
        <div class="send-message">
          <v-gravatar :email="user.email" alt="Avatar" :size="35" default-img="mm" class="send-message-avatar"/>
          <input style="resize: none;" placeholder="Type in your message" v-model="message"
                 v-on:keyup.13="sendMessage()">
          <svg v-on:click="sendMessage()" class="send-icon" version="1.1" id="Capa_1" xmlns="http://www.w3.org/2000/svg"
               xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px" viewBox="0 0 512 512"
               style="enable-background:new 0 0 512 512;" xml:space="preserve">
          <g>
            <g>
              <path d="M507.6,4.4c-4.2-4.2-10.6-5.5-16.2-3.3L9.4,193.9c-5.5,2.2-9.2,7.5-9.4,13.4c-0.2,5.9,3.1,11.4,8.4,14l190.1,92.2
                                                                                                                                                   l92.2,190.1c2.5,5.2,7.8,8.5,13.5,8.5c0.2,0,0.4,0,0.5,0c5.9-0.2,11.2-3.9,13.4-9.4l192.8-482C513.2,15,511.9,8.6,507.6,4.4z
                                                                                                                                                    M52.1,209.1l382.6-153l-228,228L52.1,209.1z M302.9,459.9l-75-154.6l228-228L302.9,459.9z"/>
            </g>
          </g>
        </svg>
        </div>
      </div>
    </no-ssr>
  </div>
</template>

<script>
  import About from "../main/About";
  import FAQ from "../main/FAQ";
  import Chat from "../main/Chat";
  import md5 from 'md5'

  export default {
    name: "Sidebar",
    props: ['webinar', 'total_seconds', 'user', 'sortMessages', 'webinar_name'],
    components: {FAQ, About, Chat},
    data() {
      return {
        title: '',
        message: '',
        bannerStatus: false,
        faqLink: false,
        packages: '',
        viewers_count: 95,
        unreadMessages: NaN,
        banner_time: '00 : 00 : 00',
        minimizeBanner: false,
        chatCursor: false,
        redirect_url: ''
      }
    },
    head() {
      return {
        title: this.title,
      }
    },
    created() {
      this.title = this.webinar.title;
    },
    mounted() {
      setTimeout(() => {
        let json_url = this.webinar.chat.banner.button.url;
        this.redirect_url = json_url.replace("XXX", this.user.cbid);
      }, 3000);
      let intervalID = setInterval(() => {
        let elapsed = jwplayer("jwVideo").getPosition();
        if (elapsed > 0) {
          this.showBanner();
          clearInterval(intervalID);
        }
      }, 500)
      this.viewers();
    },
    watch: {
      unreadMessages(newName) {
        localStorage.unread = newName;
      }
    },
    methods: {
      hasUnread(){
        if (localStorage.getItem("unread") !== null) {
          setInterval(() => {
            this.unreadMessages = localStorage.unread;
            if (isNaN(this.unreadMessages) || this.unreadMessages === undefined) {
              this.title = this.webinar.title;
            } else if (parseInt(this.unreadMessages) > 99) {
              this.title = this.webinar.title + ' (99+)'
            } else {
              this.title = this.webinar.title + ' (' + this.unreadMessages + ')'
            }
          }, 1000);
        }
      },
      showAbout() {
        let faq = document.getElementById("faq");
        let about = document.getElementById("about");
        if (!faq.classList.contains('open')) {
          if (about.classList.contains('open')) {
            localStorage.removeItem('unread');
            this.chatCursor = false;
          } else {
            localStorage.unread = 0;
            this.chatCursor = true;
          }
        }
        about.classList.toggle('open');
        faq.classList.remove('open');
        this.hasUnread()
      },
      showFAQ() {
        let faq = document.getElementById("faq");
        let about = document.getElementById("about");
        if (!about.classList.contains('open')) {
          if (faq.classList.contains('open')) {
            localStorage.removeItem('unread');
            this.chatCursor = false;
          } else {
            localStorage.unread = 0;
            this.chatCursor = true;
          }
        }
        faq.classList.toggle('open');
        about.classList.remove('open');
        this.hasUnread()
      },
      toChat() {
        let faq = document.getElementById("faq");
        let about = document.getElementById("about");
        faq.classList.remove('open');
        about.classList.remove('open');
        localStorage.removeItem('unread');
        this.title = this.webinar.title;
      },
      sendMessage() {
        if (this.message !== '') {

          let full_name  = this.user.first_name;
          if (this.user.last_name !== '') {
            full_name = this.user.first_name + ' ' + this.user.last_name;
          }

          let email_hash = md5(this.user.email);
          let avatar = 'https://www.gravatar.com/avatar/'+email_hash;

          document.getElementsByClassName('messages_height')[0].insertAdjacentHTML('beforeend', '<div class="chat-message">\n' +
            '          <img src="'+avatar+'" class="my_avatar"/>\n' +
            '          <div class="item">\n' +
            '            <div class="card chat-message-txt--colored">\n' +
            '              <div class="card-wrapper">\n' +
            '                <div class="top">\n' +
            '                  <div class="user">\n' +
            '                    <span class="name">'+full_name+'</span>\n' +
            '                  </div>\n' +
            '                </div>\n' +
            '                <div>\n' +
            '                  <p class="chat-message-txt ">'+ this.message.replace("\\n", " ")+'</p>\n' +
            '                </div>\n' +
            '              </div>\n' +
            '            </div>\n' +
            '            <span class="time-sent">Just now</span>\n' +
            '          </div>\n' +
            '        </div>');


          let current_time = jwplayer("jwVideo").getPosition();
          let sec_num = parseInt(current_time, 10);
          let minutes = Math.floor(sec_num / 60);
          let seconds = sec_num - (minutes * 60);

          let comment_data;
          let queryString = window.location.search;
          let urlParams = new URLSearchParams(queryString);
          if(urlParams.get('token') != null){
            let token = atob(urlParams.get('token'));
            let current_user = JSON.parse(token);
            comment_data = {
              min: minutes,
              second: seconds,
              name: full_name,
              email: current_user.email,
              comment: this.message.replace("\n", " "),
              from_user: 1
            };
          }else{
            comment_data = {
              min: minutes,
              second: seconds,
              name: 'User',
              comment: this.message.replace("\n", " "),
              from_user: 1
            };
          }
          if (localStorage.messages) {
            let user_messages = JSON.parse(localStorage.messages);
            let size = Object.keys(user_messages).length;
            user_messages[size] = comment_data;
            localStorage.messages = JSON.stringify(user_messages);
          } else {
            let user_messages = [];
            user_messages[0] = comment_data;
            localStorage.messages = JSON.stringify(user_messages);
          }

          this.message = '';
          let messages = document.getElementsByClassName('chat-messages')[0];
          messages.scrollTop = messages.scrollHeight;
        }
      },
      showBanner() {
        setTimeout(() => {
          if (this.webinar.chat) {
            this.timeCounter();
          }
        }, 1000);
        let adTime;
        let elapsed = jwplayer("jwVideo").getPosition();
        if (this.webinar.chat.banner.time < elapsed) {
          adTime = 3000
        } else {
          let current_time = this.webinar.chat.banner.time - elapsed;
          adTime = current_time * 1000
        }
        setTimeout(() => {
          this.faqLink = true;
          this.packagesRemaining();
          if (localStorage.getItem("minimizeBanner") === null) {
            this.bannerStatus = true;
          } else if (localStorage.minimizeBanner === 'true') {
            this.bannerStatus = false;
            this.minimizeBanner = true;
          } else {
            this.bannerStatus = true;
            this.minimizeBanner = false;
          }
        }, adTime);
      },
      timeCounter() {
        this.packages = this.webinar.chat.banner.packages
        let x = setInterval(() => {
          let distance = jwplayer("jwVideo").getPosition();
          let secs = this.total_seconds - Math.round(distance);

          let sec_num = parseInt(secs, 10);
          let hours = Math.floor(sec_num / 3600);
          let minutes = Math.floor((sec_num - (hours * 3600)) / 60);
          let seconds = sec_num - (hours * 3600) - (minutes * 60);

          if (hours < 10) {
            hours = "0" + hours
          }
          if (minutes < 10) {
            minutes = "0" + minutes
          }
          if (seconds < 10) {
            seconds = "0" + seconds
          }

          this.banner_time = hours + " : " + minutes + " : " + seconds;

          if (secs === 0) {
            clearInterval(x);
            this.banner_time = "EXPIRED";
            window.location = this.redirect_url
          }

        }, 1000);
      },
      packagesRemaining() {

        let elapsed = jwplayer("jwVideo").getPosition();
        let ave = Math.round((this.total_seconds - this.webinar.chat.banner.time) / this.webinar.chat.banner.packages);
        let current_time = this.total_seconds - elapsed;
        this.packages = Math.round(current_time / ave);

        (function loop() {
          let average = Math.round((this.total_seconds - this.webinar.chat.banner.time) / this.webinar.chat.banner.packages) * 1000;
          setTimeout(() => {
            if (this.packages > 1) {
              this.packages--;
              loop.call(this);
            }
          }, average);
        }.call(this));
      },
      viewers() {
        if (localStorage.viewers) {
          this.viewers_count = localStorage.viewers;
        }
        (function loop() {
          let min = 1;
          let max = 50;
          let num = min + Math.random() * (max + 1 - min);
          let rand = Math.floor(num) * 1000;
          setTimeout(() => {
            if (localStorage.viewers_stabil) {
              if (this.viewers_count === 102) {
                this.viewers_count++;
              } else if (this.viewers_count === 107) {
                this.viewers_count--;
              } else {
                let rnd = Math.random() >= 0.5;
                if (rnd) {
                  this.viewers_count++
                } else {
                  this.viewers_count--;
                }
              }
              localStorage.viewers = this.viewers_count;
            } else if (this.viewers_count > 102 && localStorage.viewers_to_down) {
              this.viewers_count--;
              localStorage.viewers = this.viewers_count;
              if (this.viewers_count === 102) {
                localStorage.viewers_stabil = 1
              }
            } else if (this.viewers_count < 175) {
              if (this.viewers_count < 170) {
                if (rand >= 35000) {
                  this.viewers_count = parseInt(this.viewers_count) + 3
                } else if (rand >= 17500) {
                  this.viewers_count = parseInt(this.viewers_count) + 2
                }
              } else {
                this.viewers_count++;
              }

              localStorage.viewers = this.viewers_count;
            } else {
              localStorage.viewers_to_down = 1;
              this.viewers_count--;
              localStorage.viewers = this.viewers_count;
            }
            loop.call(this);
          }, rand);
        }.call(this));
      },
      toggleAd() {
        this.bannerStatus = !this.bannerStatus;
        this.minimizeBanner = !this.minimizeBanner;
        localStorage.minimizeBanner = localStorage.getItem("minimizeBanner") === null || localStorage.minimizeBanner == 'false';
      },
    },
  }
</script>
