<template>
  <div
    v-show="!isFullScreen"
    id="aside"
    style="overflow-x:hidden; scrollbar-width: none;"
    class="w-[292px] fixed top-0 z-30 h-screen transition-position bg-[#f4f5f7] font-SfProDisplayNormal text-sm"
  >
    <AsideMenuSkeleton v-if="status == 'loading'" />
    <div v-if="status == 'success'">
      <div class="mt-[20px]">
        <DatePicker
          v-if="lastTab === 'tasks'"
          id="step4"
          ref="calendarclass"
          v-model="dateToday"
          dot="true"
          class="border-none pl-[22px] pr-[16px] calendar-nav-custom"
          :style="{ backgroundColor: datePickerBG }"
          show-weeknumbers="left"
          days="-1"
          color="#CCC"
          week-from-end="6"
          is-expanded
          :locale="getNavigatorLanguage"
          :masks="{ weekdays: 'WWW' }"
          :attributes="attrs"
          :is-dark="isDark"
          mode="single"
          is-inline
          in-next-month="true"
          in-month="true"
          in-prev-month="true"
          @dayclick="onDayClick"
        />
      </div>
      <div class="my-[10px]">
        <template v-for="(menuGroup, index) in menu">
          <div
            v-if="typeof menuGroup === 'string'"
            :key="`a-${index}`"
            class="my-2"
          >
            <hr
              :key="`a-${index}`"
              class="text-xs mx-3 custom-border-divider"
              :class="[ asideMenuLabelStyle ]"
            >
          </div>
          <aside-menu-list
            v-else
            :key="`b-${index}`"
            :menu="menuGroup"
            :assigments="assigments"
            @menu-click="menuClick"
            @assigments-click="assigmentsClick"
          />
        </template>
        <ul
          v-if="(favoriteBoards || favoriteProjects) && lastTab === 'tasks'"
          class="mt-[20px] mb-10"
        >
          <li
            v-for="project in favoriteProjects"
            :key="project.uid"
            class="h-[40px] flex items-center cursor-pointer hover:bg-white hover:rounded-[10px] mx-[16px]"
            @click.prevent="goToProject(project)"
          >
            <div class="relative flex items-center w-[26px] justify-center ml-[8px] mr-[8px]">
              <svg
                v-if="project.members?.length > 1"
                class="flex-none"
                width="20"
                height="20"
                viewBox="0 0 20 20"
                fill="none"
                xmlns="http://www.w3.org/2000/svg"
              >
                <path
                  fill-rule="evenodd"
                  clip-rule="evenodd"
                  d="M17.4486 4.19718V5.13099C18.208 5.21549 18.804 5.8831 18.8 6.69437V15.4282C18.8 16.2944 18.1182 17 17.2812 17H4.31874C3.48179 17 2.79999 16.2944 2.79999 15.4282V3.57183C2.79999 2.70563 3.48179 2 4.31874 2H8.95665C9.16895 2 9.369 2.11831 9.48332 2.30423L9.67112 2.62535H15.9298C16.7668 2.62535 17.4486 3.33099 17.4486 4.19718ZM4.32282 3.27183C4.1636 3.27183 4.03295 3.40704 4.03295 3.57183H4.02887V15.4282C4.02887 15.593 4.15952 15.7282 4.31874 15.7282H17.2894C17.4486 15.7282 17.5792 15.593 17.5792 15.4282V6.69014C17.5792 6.52535 17.4486 6.39014 17.2894 6.39014H10.8061C10.5897 6.39014 10.3897 6.27183 10.2794 6.08592L8.61371 3.27183H4.32282ZM11.145 5.11831H16.2156V4.19718C16.2156 4.03239 16.085 3.89718 15.9258 3.89718H10.4223L11.145 5.11831Z"
                  fill="black"
                  fill-opacity="0.5"
                />
                <path
                  fill-rule="evenodd"
                  clip-rule="evenodd"
                  d="M8.30582 12.9311C8.25247 12.6478 8.13967 12.3901 7.98307 12.1718C7.80076 11.9178 7.74959 11.5587 7.79848 11.246C7.91225 10.5181 7.39682 9.75313 6.62572 9.75685C5.85815 9.75313 5.34065 10.518 5.45336 11.2458C5.50181 11.5587 5.45063 11.9176 5.2676 12.1711C5.10992 12.3895 4.99648 12.6475 4.94562 12.9311L4.8738 13.3144C4.80752 13.6681 5.06842 13.9969 5.41549 13.9969H7.83595C8.18302 13.9969 8.44393 13.6681 8.37764 13.3144L8.30582 12.9311ZM6.23066 11.0321C6.23066 10.7994 6.40865 10.6086 6.62572 10.6086C7.15102 10.6319 7.15102 11.4324 6.62572 11.4557C6.40865 11.4557 6.23066 11.2648 6.23066 11.0321ZM6.36206 13.1498C6.02074 13.1498 5.7527 12.7996 6.00164 12.5571C6.34666 12.221 6.90044 12.221 7.24546 12.5571C7.4944 12.7996 7.22636 13.1498 6.88504 13.1498H6.36206ZM12.5612 12.9311C12.5078 12.6478 12.395 12.3901 12.2384 12.1718C12.0561 11.9178 12.0049 11.5587 12.0538 11.246C12.1676 10.5181 11.6522 9.75313 10.8811 9.75685C10.1135 9.75313 9.59598 10.518 9.70869 11.2458C9.75714 11.5587 9.70597 11.9176 9.52294 12.1711C9.36526 12.3895 9.25182 12.6475 9.20095 12.9311L9.12914 13.3144C9.06285 13.6681 9.32376 13.9969 9.67082 13.9969H12.0913C12.4384 13.9969 12.6993 13.6681 12.633 13.3144L12.5612 12.9311ZM10.486 11.0321C10.486 10.7994 10.664 10.6086 10.8811 10.6086C11.4064 10.6319 11.4064 11.4324 10.8811 11.4557C10.664 11.4557 10.486 11.2648 10.486 11.0321ZM10.6174 13.1498C10.2761 13.1498 10.008 12.7996 10.257 12.5571C10.602 12.221 11.1558 12.221 11.5008 12.5571C11.7497 12.7996 11.4817 13.1498 11.1404 13.1498H10.6174ZM16.4937 12.1718C16.6503 12.3901 16.7631 12.6478 16.8165 12.9311L16.8883 13.3144C16.9546 13.6681 16.6937 13.9969 16.3466 13.9969H13.9262C13.5791 13.9969 13.3182 13.6681 13.3845 13.3144L13.4563 12.9311C13.5072 12.6475 13.6206 12.3895 13.7783 12.1711C13.9613 11.9176 14.0125 11.5587 13.964 11.2458C13.8513 10.518 14.3688 9.75313 15.1364 9.75685C15.9075 9.75313 16.4229 10.5181 16.3091 11.246C16.2603 11.5587 16.3114 11.9178 16.4937 12.1718ZM15.1364 10.6086C14.9193 10.6086 14.7413 10.7994 14.7413 11.0321C14.7413 11.2648 14.9193 11.4557 15.1364 11.4557C15.6617 11.4324 15.6617 10.6319 15.1364 10.6086ZM14.5123 12.5571C14.2634 12.7996 14.5314 13.1498 14.8727 13.1498H15.3957C15.737 13.1498 16.0051 12.7996 15.7561 12.5571C15.4111 12.221 14.8573 12.221 14.5123 12.5571Z"
                  fill="black"
                  fill-opacity="0.5"
                />
              </svg>
              <svg
                v-else
                class="flex-none"
                width="20"
                height="20"
                viewBox="0 0 20 20"
                fill="none"
                xmlns="http://www.w3.org/2000/svg"
              >
                <path
                  d="M16.6486 5.13099V4.19718C16.6486 3.33099 15.9668 2.62535 15.1299 2.62535H8.87113L8.68333 2.30423C8.56901 2.11831 8.36896 2 8.15666 2H3.51875C2.68181 2 2 2.70563 2 3.57183V15.4282C2 16.2944 2.68181 17 3.51875 17H16.4812C17.3182 17 18 16.2944 18 15.4282V6.69437C18.0041 5.8831 17.408 5.21549 16.6486 5.13099ZM3.23297 3.57183C3.23297 3.40704 3.36361 3.27183 3.52284 3.27183H7.81372L9.47945 6.08592C9.58968 6.27183 9.78973 6.39014 10.0061 6.39014H16.4894C16.6486 6.39014 16.7793 6.52535 16.7793 6.69014V15.4282C16.7793 15.593 16.6486 15.7282 16.4894 15.7282H3.51875C3.35953 15.7282 3.22888 15.593 3.22888 15.4282V3.57183H3.23297ZM15.4157 5.11831H10.345L9.62234 3.89718H15.1258C15.285 3.89718 15.4157 4.03239 15.4157 4.19718V5.11831Z"
                  fill="black"
                  fill-opacity="0.5"
                />
              </svg>
            </div>
            <p class="font-roboto text-[13px] leading-[15px] font-medium text-[#606061]">
              {{ project.name }}
            </p>
          </li>
          <li
            v-for="board in favoriteBoards"
            :key="board.uid"
            class="h-[40px] flex items-center cursor-pointer hover:bg-white hover:rounded-[10px] mx-[16px]"
            @click.prevent="goToBoard(board)"
          >
            <div class="flex items-center w-[26px] justify-center ml-[8px] mr-[8px]">
              <svg
                v-if="board.members && Object.keys(board.members).length > 1"
                class="flex-none"
                width="20"
                height="20"
                viewBox="0 0 20 20"
                fill="none"
                xmlns="http://www.w3.org/2000/svg"
              >
                <path
                  fill-rule="evenodd"
                  clip-rule="evenodd"
                  d="M3.2 18C2.53878 18 2 17.4623 2 16.8005V4.93771C2 4.27591 2.53878 3.7382 3.2 3.7382H6.08163V2.88613C6.08163 2.59659 6.3102 2.36496 6.59592 2.36496H8.18775L8.22857 2.25742C8.4898 1.50462 9.20816 1 10 1C10.7918 1 11.5102 1.50462 11.7714 2.25742L11.8122 2.36496H13.4041C13.6898 2.36496 13.9184 2.59659 13.9184 2.88613V3.7382H16.8C17.4612 3.7382 18 4.27591 18 4.93771V16.8005C18 17.4623 17.4612 18 16.8 18H3.2ZM3.03673 16.9659H16.9714V4.77226H13.9184V5.62433C13.9184 5.91387 13.6898 6.1455 13.4041 6.1455H6.59592C6.3102 6.1455 6.08163 5.91387 6.08163 5.62433V4.77226H3.03673V16.9659ZM7.11837 5.10316H12.8898V3.4073H11.3633C11.0776 3.4073 10.849 3.17567 10.849 2.88613C10.849 2.4146 10.4735 2.03406 10.0082 2.03406C9.54286 2.03406 9.15918 2.4146 9.15918 2.88613C9.15918 3.17567 8.93061 3.4073 8.6449 3.4073H7.11837V5.10316Z"
                  fill="black"
                  fill-opacity="0.5"
                />
                <path
                  fill-rule="evenodd"
                  clip-rule="evenodd"
                  d="M7.50583 13.9311C7.45249 13.6478 7.33968 13.3901 7.18308 13.1718C7.00077 12.9178 6.9496 12.5587 6.99849 12.246C7.11226 11.5181 6.59683 10.7531 5.82573 10.7568C5.05816 10.7531 4.54066 11.518 4.65337 12.2458C4.70182 12.5587 4.65065 12.9176 4.46761 13.1711C4.30993 13.3895 4.1965 13.6475 4.14563 13.9311L4.07381 14.3144C4.00753 14.6681 4.26844 14.9969 4.6155 14.9969H7.03596C7.38303 14.9969 7.64394 14.6681 7.57765 14.3144L7.50583 13.9311ZM5.43067 12.0321C5.43067 11.7994 5.60867 11.6086 5.82573 11.6086C6.35103 11.6319 6.35103 12.4324 5.82573 12.4557C5.60867 12.4557 5.43067 12.2648 5.43067 12.0321ZM5.56207 14.1498C5.22075 14.1498 4.95271 13.7996 5.20165 13.5571C5.54667 13.221 6.10045 13.221 6.44547 13.5571C6.69441 13.7996 6.42637 14.1498 6.08505 14.1498H5.56207ZM11.7612 13.9311C11.7078 13.6478 11.595 13.3901 11.4384 13.1718C11.2561 12.9178 11.2049 12.5587 11.2538 12.246C11.3676 11.5181 10.8522 10.7531 10.0811 10.7568C9.31349 10.7531 8.79599 11.518 8.90871 12.2458C8.95715 12.5587 8.90598 12.9176 8.72295 13.1711C8.56527 13.3895 8.45183 13.6475 8.40097 13.9311L8.32915 14.3144C8.26286 14.6681 8.52377 14.9969 8.87084 14.9969H11.2913C11.6384 14.9969 11.8993 14.6681 11.833 14.3144L11.7612 13.9311ZM9.686 12.0321C9.686 11.7994 9.864 11.6086 10.0811 11.6086C10.6064 11.6319 10.6064 12.4324 10.0811 12.4557C9.864 12.4557 9.686 12.2648 9.686 12.0321ZM9.81741 14.1498C9.47608 14.1498 9.20805 13.7996 9.45698 13.5571C9.802 13.221 10.3558 13.221 10.7008 13.5571C10.9497 13.7996 10.6817 14.1498 10.3404 14.1498H9.81741ZM15.6937 13.1718C15.8504 13.3901 15.9632 13.6478 16.0165 13.9311L16.0883 14.3144C16.1546 14.6681 15.8937 14.9969 15.5466 14.9969H13.1262C12.7791 14.9969 12.5182 14.6681 12.5845 14.3144L12.6563 13.9311C12.7072 13.6475 12.8206 13.3895 12.9783 13.1711C13.1613 12.9176 13.2125 12.5587 13.164 12.2458C13.0513 11.518 13.5688 10.7531 14.3364 10.7568C15.1075 10.7531 15.6229 11.5181 15.5092 12.246C15.4603 12.5587 15.5114 12.9178 15.6937 13.1718ZM14.3364 11.6086C14.1193 11.6086 13.9413 11.7994 13.9413 12.0321C13.9413 12.2648 14.1193 12.4557 14.3364 12.4557C14.8617 12.4324 14.8617 11.6319 14.3364 11.6086ZM13.7123 13.5571C13.4634 13.7996 13.7314 14.1498 14.0727 14.1498H14.5957C14.937 14.1498 15.2051 13.7996 14.9561 13.5571C14.6111 13.221 14.0573 13.221 13.7123 13.5571Z"
                  fill="black"
                  fill-opacity="0.5"
                />
              </svg>
              <svg
                v-else
                class="flex-none"
                width="20"
                height="20"
                viewBox="0 0 20 20"
                fill="none"
                xmlns="http://www.w3.org/2000/svg"
              >
                <path
                  fill-rule="evenodd"
                  clip-rule="evenodd"
                  d="M3.2 18C2.53878 18 2 17.4623 2 16.8005V4.93771C2 4.27591 2.53878 3.7382 3.2 3.7382H6.08163V2.88613C6.08163 2.59659 6.3102 2.36496 6.59592 2.36496H8.18775L8.22857 2.25742C8.4898 1.50462 9.20816 1 10 1C10.7918 1 11.5102 1.50462 11.7714 2.25742L11.8122 2.36496H13.4041C13.6898 2.36496 13.9184 2.59659 13.9184 2.88613V3.7382H16.8C17.4612 3.7382 18 4.27591 18 4.93771V16.8005C18 17.4623 17.4612 18 16.8 18H3.2ZM3.03673 16.9659H16.9714V4.77226H13.9184V5.62433C13.9184 5.91387 13.6898 6.1455 13.4041 6.1455H6.59592C6.3102 6.1455 6.08163 5.91387 6.08163 5.62433V4.77226H3.03673V16.9659ZM7.11837 5.10316H12.8898V3.4073H11.3633C11.0776 3.4073 10.849 3.17567 10.849 2.88613C10.849 2.4146 10.4735 2.03406 10.0082 2.03406C9.54286 2.03406 9.15918 2.4146 9.15918 2.88613C9.15918 3.17567 8.93061 3.4073 8.6449 3.4073H7.11837V5.10316ZM13.3061 14.2608C12.9633 14.2608 12.6939 13.9796 12.6939 13.6404V12.8545C12.6939 12.5071 12.9714 12.2341 13.3061 12.2341H15.0531C15.3959 12.2341 15.6653 12.5153 15.6653 12.8545V13.6404C15.6653 13.9878 15.3878 14.2608 15.0531 14.2608H13.3061ZM9.22449 14.2608C8.8898 14.2608 8.61225 13.9796 8.61225 13.6404V12.8545C8.61225 12.5071 8.8898 12.2341 9.22449 12.2341H10.9714C11.3143 12.2341 11.5837 12.5153 11.5837 12.8545V13.6404C11.5837 13.9878 11.3061 14.2608 10.9714 14.2608H9.22449ZM4.94694 14.2608C4.60408 14.2608 4.33469 13.9796 4.33469 13.6404V12.8545C4.33469 12.5071 4.61224 12.2341 4.94694 12.2341H6.69388C7.02857 12.2341 7.30612 12.5153 7.30612 12.8545V13.6404C7.30612 13.9878 7.02857 14.2608 6.69388 14.2608H4.94694ZM13.3061 10.3231C12.9633 10.3231 12.6939 10.0419 12.6939 9.70268V8.90852C12.6939 8.56934 12.9714 8.28808 13.3061 8.28808H15.0531C15.3959 8.28808 15.6653 8.56934 15.6653 8.90852V9.6944C15.6653 10.0336 15.3878 10.3148 15.0531 10.3148H13.3061V10.3231ZM9.22449 10.3231C8.8898 10.3231 8.61225 10.0419 8.61225 9.70268V8.90852C8.61225 8.56934 8.8898 8.28808 9.22449 8.28808H10.9714C11.3143 8.28808 11.5837 8.56934 11.5837 8.90852V9.6944C11.5837 10.0336 11.3061 10.3148 10.9714 10.3148H9.22449V10.3231ZM4.94694 10.3231C4.60408 10.3231 4.33469 10.0419 4.33469 9.70268V8.90852C4.33469 8.56934 4.61224 8.28808 4.94694 8.28808H6.69388C7.02857 8.28808 7.30612 8.56934 7.30612 8.90852V9.6944C7.30612 10.0336 7.02857 10.3148 6.69388 10.3148H4.94694V10.3231Z"
                  fill="black"
                  fill-opacity="0.5"
                />
              </svg>
            </div>
            <p class="font-roboto text-[13px] leading-[15px] font-medium text-[#606061]">
              {{ board.name.length < 26 ? board.name : board.name.substring(0, 26) + '...' }}
            </p>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import { DatePicker } from 'v-calendar'
import AsideMenuList from '@/components/AsideMenuList.vue'
import AsideMenuSkeleton from '@/components/AsideMenuSkeleton.vue'
import 'v-calendar/dist/style.css'
import { UID_TO_ACTION } from '@/store/helpers/functions'

import warn from '@/icons/warn.js'
import { mdiMenu } from '@mdi/js'

import * as TASK from '@/store/actions/tasks'
import * as CARD from '@/store/actions/cards'

export default {
  components: {
    DatePicker,
    AsideMenuSkeleton,
    AsideMenuList
  },
  props: {
    menu: {
      type: Array,
      default: () => []
    }
  },
  data () {
    return {
      newDayTimerID: 0,
      dateToday: '',
      mdiMenu,
      currentDay: null,
      visitedDay: null,
      userParentId: null,
      warn,
      showFreeModal: false,
      modalOneActive: false,
      lastVisitedDate: this.navStack && this.navStack.length && this.navStack[this.navStack.length - 1] && this.navStack[this.navStack.length - 1].uid && this.navStack[this.navStack.length - 1].uid === '901841d9-0016-491d-ad66-8ee42d2b496b' && this.navStack[this.navStack.length - 1].param ? new Date(this.navStack[this.navStack.length - 1].param) : new Date(),
      currentSettingsTab: 'account'
    }
  },
  computed: {
    status () {
      return this.$store.state.navigator.status
    },
    lastTab () {
      return this.$store.state.navigator.lastTab
    },
    isFullScreen () {
      return this.$store.state.isFullScreen
    },
    isAsideMobileExpanded () {
      return this.$store.state.isAsideMobileExpanded
    },
    isPropertiesMobileExpanded () {
      return this.$store.state.isPropertiesMobileExpanded
    },
    isDark () {
      return this.$store.state.darkMode
    },
    navStack () {
      return this.$store.state.navbar.navStack
    },
    datePickerBG () {
      return this.isDark ? 'rgb(31 41 55)' : '#f4f5f7'
    },
    attrs () {
      return this.$store.getters.attrsCalendar
    },
    user () {
      return this.$store.state.user.user
    },
    storeNavigator () {
      return this.$store.getters.sortedNavigator
    },
    getNavigatorLanguage () {
      return (navigator.languages && navigator.languages.length) ? navigator.languages[0] : navigator.userLanguage || navigator.language || navigator.browserLanguage || 'en'
    },
    favoriteBoards () {
      const arr = []
      const boards = this.$store.state.boards.boards
      Object.keys(boards).forEach(key => {
        if (boards[key].favorite === 1) {
          arr.push(boards[key])
        }
      })
      return arr.sort((board1, board2) => { return board1.name.localeCompare(board2.name) })
    },
    favoriteProjects () {
      const arr = []
      const projects = this.$store.state.projects.projects
      Object.keys(projects).forEach(key => {
        if (projects[key].favorite === 1) {
          arr.push(projects[key])
        }
      })
      return arr.sort((project1, project2) => { return project1.name.localeCompare(project2.name) })
    },
    assigments () {
      return { delegate_iam: this.storeNavigator.delegate_iam, delegate_to_me: this.storeNavigator.delegate_to_me }
    }
  },
  mounted () {
    let currDate = new Date()
    currDate.setHours(0, 0, 0, 0)
    this.newDayTimerID = setInterval(() => {
      const newDate = new Date()
      newDate.setHours(0, 0, 0, 0)
      if (currDate.getTime() !== newDate.getTime()) {
        currDate = newDate
        this.onNewDay()
      }
    }, 1000)
  },
  beforeUnmount () {
    clearInterval(this.newDayTimerID)
  },
  methods: {
    dateToLabelFormat (calendarDate) {
      const day = calendarDate.getDate()
      const month = calendarDate.toLocaleString('default', { month: 'short' })
      const weekday = calendarDate.toLocaleString('default', { weekday: 'short' })
      return day + ' ' + month + ', ' + weekday
    },
    asideLgClose () {
      this.$store.dispatch('asideLgToggle', false)
    },
    // TODO: clean up messy logic
    menuClick (event, item) {
      // Если уже находимся на этой вкладке игнорировать дальнейший код
      if (this.checkOnWhichTab(item)) {
        return
      }
      localStorage.setItem('lastTab', 'tasks')
      this.userParentId = null
      this.visitedDay = ''
      if (item.uid === '901841d9-0016-491d-ad66-8ee42d2b496b') {
        this.dateToday = new Date()
      }

      if (item.path === 'new_private_boards' || item.path === 'new_private_projects') {
        this.$store.state.navigator.submenu.status = true
        this.$store.state.navigator.submenu.path = item.path
        return
      } else {
        this.$store.state.navigator.submenu.status = false
      }

      console.log(item)
      // скрывать навбар при онбординге
      // if (this.$store.state.onboarding.visitedModals) {
      //   this.$store.state.onboarding.hideNavBar = false
      //   if (!this.$store.state.onboarding.visitedModals?.includes(this.$store.state.onboarding.hintUid[item.uid])) {
      //     console.log('hide')
      //     this.$store.state.onboarding.hideNavBar = true
      //   }
      // }
      if (this.isPropertiesMobileExpanded) {
        this.$store.dispatch('asidePropertiesToggle', false)
      }
      if (this.isAsideMobileExpanded) {
        this.$store.dispatch('asideMobileToggle', false)
      }

      if (['account', 'tarif', 'option', 'karma', 'support'].includes(item.type)) {
        this.$store.state.navigator.currentSettingsTab = item.type
        const navElem = {
          name: item.label,
          key: 'greedSource',
          value: { uid: item.uid, param: null },
          greedPath: item.type
        }
        localStorage.setItem('currentSettingsTab', item.type)
        this.$store.commit('updateStackWithInitValue', navElem)
        return
      }

      // do it now
      if (item.uid === '2cf6b167-6506-4b05-bc34-70a8d88e3b25') {
        window.ym(89796698, 'reachGoal', 'doitnow')
        if (this.user.tarif !== 'alpha' && this.user.tarif !== 'trial') {
          this.showFreeModal = true
          return
        }
        const navElem = {
          name: item.label,
          key: 'greedSource',
          value: { uid: item.uid, param: null },
          greedPath: 'doitnow'
        }
        this.$store.commit('updateStackWithInitValue', navElem)
        this.$store.commit('basic', { key: 'mainSectionState', value: 'greed' })
        this.$store.commit('basic', { key: 'greedPath', value: 'doitnow' })
        return
      }

      // other
      if (item.uid === '757be87d-c269-40e0-b224-6b2bb0e4f97d') {
        const navElem = {
          name: item.label,
          key: 'greedSource',
          value: { uid: item.uid, param: null },
          greedPath: 'other'
        }
        this.$store.commit('updateStackWithInitValue', navElem)
        this.$store.commit('basic', { key: 'mainSectionState', value: 'greed' })
        this.$store.commit('basic', { key: 'greedPath', value: 'other' })
        // отображаем навбар в прочее
        this.$store.state.onboarding.hideNavBar = false
        return
      }

      // colors & tags
      if (item.uid === 'ed8039ae-f3de-4369-8f32-829d401056e9' || item.uid === '00a5b3de-9474-404d-b3ba-83f488ac6d30') {
        this.$store.commit('basic', { key: 'mainSectionState', value: 'greed' })
        this.$store.commit('basic', { key: 'greedPath', value: item.path })
        const navElem = {
          name: item.label,
          key: 'greedSource',
          greedPath: item.path,
          value: this.storeNavigator[item.path]?.items
        }
        this.$store.commit('updateStackWithInitValue', navElem)
        this.$store.commit('basic', { key: 'greedSource', value: this.storeNavigator[item.path]?.items })
        return
      }

      // Tasks list source
      if (UID_TO_ACTION[item.uid] && item.type === 'uid') {
        this.$store.dispatch(UID_TO_ACTION[item.uid])
        const navElem = {
          name: item.label,
          key: 'taskListSource',
          value: { uid: item.uid, param: new Date() },
          typeVal: new Date(),
          type: 'date'
        }
        this.$store.commit('updateStackWithInitValue', navElem)
        if (item.uid === '901841d9-0016-491d-ad66-8ee42d2b496b') { this.lastVisitedDate = new Date() } // desktop check
        this.$store.commit('basic', { key: 'taskListSource', value: { uid: item.uid, param: null } })
        this.$store.commit('basic', { key: 'mainSectionState', value: 'tasks' })
      // Grid source (projects, employees, colors, tags)
      } else {
        this.$store.commit('basic', { key: 'mainSectionState', value: 'greed' })
        this.$store.commit('basic', { key: 'greedPath', value: item.path })
        if (item.path === 'new_private_projects' || item.path === 'new_emps' || item.path === 'new_delegate' || item.path === 'new_private_boards') {
          const navElem = {
            name: item.label,
            key: 'greedSource',
            greedPath: item.path,
            value: this.storeNavigator[item.path]
          }
          this.$store.commit('updateStackWithInitValue', navElem)
          this.$store.commit('basic', { key: 'greedSource', value: this.storeNavigator[item.path] })
        } else {
          console.log(item.uid)
          const navElem = {
            name: item.label,
            key: 'greedSource',
            greedPath: item.path,
            value: this.storeNavigator[item.path]?.items
          }
          this.$store.commit('updateStackWithInitValue', navElem)
          this.$store.commit('basic', { key: 'greedSource', value: this.storeNavigator[item.path]?.items })
        }
      }
    },
    onDayClick (day) {
      if (this.checkOnWhichDay(day)) {
        return
      }
      this.userParentId = null
      this.resetLastTab()
      this.$store.dispatch('asidePropertiesToggle', false)
      this.$store.dispatch(TASK.TASKS_REQUEST, new Date(day.date))
      const navElem = {
        name: this.dateToLabelFormat(day.date),
        key: 'taskListSource',
        value: { uid: '901841d9-0016-491d-ad66-8ee42d2b496b', param: new Date(day.date) },
        typeVal: new Date(day.date),
        type: 'date'
      }
      this.$store.commit('updateStackWithInitValue', navElem)
      this.lastVisitedDate = new Date() // desktop check
      this.$store.commit('basic', { key: 'taskListSource', value: { uid: '901841d9-0016-491d-ad66-8ee42d2b496b', param: new Date(day.date) } })
      this.$store.commit('basic', { key: 'mainSectionState', value: 'tasks' })
    },
    goToBoard (board) {
      if (this.checkOnWhichTab(board)) {
        return
      }
      this.userParentId = null
      if (this.isPropertiesMobileExpanded) {
        this.$store.dispatch('asidePropertiesToggle', false)
      }
      if (this.isAsideMobileExpanded) {
        this.$store.dispatch('asideMobileToggle', false)
      }

      this.$store.commit('basic', { key: 'mainSectionState', value: 'greed' })
      const path = 'new_private_boards'
      const el = {
        greedPath: path,
        key: 'greedSource',
        name: 'Доски',
        value: this.storeNavigator[path]
      }
      this.$store.commit('updateStackWithInitValue', el)

      this.$store.dispatch(CARD.BOARD_CARDS_REQUEST, board.uid)
      this.$store.commit('basic', {
        key: 'cardSource',
        value: { uid: board.global_property_uid, param: board.uid }
      })

      const navElem = {
        name: board.name,
        key: 'greedSource',
        uid: board.uid,
        global_property_uid: board.global_property_uid,
        greedPath: 'boards_children',
        value: board.children
      }

      this.$store.commit('pushIntoNavStack', navElem)
      this.$store.commit('basic', { key: 'greedSource', value: board.children })
      this.$store.commit('basic', {
        key: 'greedPath',
        value: 'boards_children'
      })
    },
    goToProject (project) {
      if (this.checkOnWhichTab(project)) {
        return
      }
      this.userParentId = null
      if (this.isPropertiesMobileExpanded) {
        this.$store.dispatch('asidePropertiesToggle', false)
      }
      if (this.isAsideMobileExpanded) {
        this.$store.dispatch('asideMobileToggle', false)
      }

      this.$store.commit('basic', { key: 'mainSectionState', value: 'greed' })
      const path = 'new_private_projects'
      const el = {
        name: 'Проекты',
        key: 'greedSource',
        greedPath: path,
        value: this.storeNavigator[path]
      }
      this.$store.commit('updateStackWithInitValue', el)

      this.$store.dispatch(TASK.PROJECT_TASKS_REQUEST, project.uid)
      this.$store.commit('basic', {
        key: 'taskListSource',
        value: { uid: project.global_property_uid, param: project.uid }
      })

      this.$store.commit(TASK.CLEAN_UP_LOADED_TASKS)

      const navElem = {
        name: project.name,
        key: 'greedSource',
        uid: project.uid,
        global_property_uid: project.global_property_uid,
        greedPath: 'projects_children',
        value: project.children
      }

      this.$store.commit('pushIntoNavStack', navElem)
      this.$store.commit('basic', { key: 'greedSource', value: project.children })
      this.$store.commit('basic', { key: 'greedPath', value: 'projects_children' })
    },
    checkOnWhichTab (item) {
      const lastNavStack = this.navStack[this.navStack.length - 1]
      if (lastNavStack?.value?.uid === item.uid ||
        lastNavStack?.uid === item.uid ||
        lastNavStack?.name === item.label ||
        (lastNavStack.name && item.name && lastNavStack?.name === item.name)) {
        return true
      }
    },
    checkOnWhichDay (day) {
      this.currentDay = day.id
      if (this.visitedDay === this.currentDay) {
        return true
      }
      this.visitedDay = this.currentDay
    },
    resetLastTab () {
      this.lastSelectedItem = null
    },
    onNewDay () {
      this.$store.commit('updateCalendarToday')
      //
    },
    assigmentsClick (user) {
      if (this.$store.state.navbar.lastSelectedAsideTab === user.uid && this.userParentId === user.parentID) {
        return
      }
      console.log(user.parentID)
      if (this.isPropertiesMobileExpanded) {
        this.$store.dispatch('asidePropertiesToggle', false)
      }

      const action = UID_TO_ACTION[user.parentID]
      if (!action) {
        console.error('UID_TO_ACTION in undefined', user.parentID)
        return
      }
      this.$store.dispatch(action, user.email)
      const navElem = {
        name: user.name,
        key: 'taskListSource',
        value: { uid: user.parentID, param: user.email }
      }
      this.$store.commit('updateStackWithInitValue', navElem)
      this.$store.commit('basic', { key: 'taskListSource', value: { uid: user.parentID, param: user.email } })
      this.$store.commit('basic', { key: 'mainSectionState', value: 'tasks' })
      this.$store.commit(TASK.CLEAN_UP_LOADED_TASKS)
      this.$store.state.navbar.lastSelectedAsideTab = user.uid
      this.userParentId = user.parentID
    }
  }
}
</script>

<style>
.vc-container.calendar-nav-custom
{
  @apply font-roboto;
}

.calendar-nav-custom .vc-weeknumber-content {
  @apply bg-white rounded-[6px] text-[#8b64bd] not-italic text-[11px] mt-0 mr-[2px] h-[24px] w-[24px];
}

.calendar-nav-custom .vc-weeks {
  @apply p-0;
}

.calendar-nav-custom .vc-arrow {
  @apply text-[#424242] w-[32px] h-[32px];
}

.calendar-nav-custom .vc-title {
  @apply text-[#424242] capitalize text-[15px] font-bold;
}

.calendar-nav-custom .vc-weekday {
  @apply text-[#424242] capitalize text-[12px] h-[34px] pt-0 pb-0 flex items-center justify-center font-bold;
}
.calendar-nav-custom .vc-weekday:nth-child(7), .vc-weekday:nth-child(8)
{
  @apply text-[#e23300];
}

.calendar-nav-custom .vc-header
{
  @apply mb-[2px] h-[32px] p-0;
}

.calendar-nav-custom .vc-arrows-container
{
  @apply h-[32px] p-0;
}

.calendar-nav-custom .vc-day
{
  @apply min-h-[30px] h-[30px] w-[30px];
}

.calendar-nav-custom .vc-day-content:focus
{
  @apply bg-[#ff9123]/70 !important;
}

.calendar-nav-custom .vc-highlight,
.calendar-nav-custom .vc-highlight:focus
{
  @apply bg-[#ff9123]/50;
}

.calendar-nav-custom .vc-day-content,
.calendar-nav-custom .vc-highlight
{
  @apply rounded-[8px] w-[30px] h-[30px] border-[#ff9123]/40 text-[#424242] text-[12px] font-medium !important;
}

.calendar-nav-custom .is-not-in-month .vc-day-content,
.calendar-nav-custom .is-not-in-month .vc-highlights,
.calendar-nav-custom .is-not-in-month .vc-day-layer,
.calendar-nav-custom .is-not-in-month .vc-highlight
{
  @apply opacity-100 pointer-events-auto text-black/50 !important;
}

.calendar-nav-custom .vc-day.weekday-7:not(.is-not-in-month) .vc-day-content,
.calendar-nav-custom .vc-day.weekday-1:not(.is-not-in-month) .vc-day-content
{
  @apply text-red-500 !important;
}

.calendar-nav-custom .dots-back
{
  background-color: #444444!important;
  height: 3px !important;
  width: 3px !important;
  position: relative !important;
  top: 11px !important;
  border-radius: 9999px !important;
}
</style>
