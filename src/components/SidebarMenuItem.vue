<template>
  <li
    v-if="item.component && !isHidden"
  >
    <component
      :is="item.component"
      v-bind="item.props"
    />
  </li>
  <li
    v-else-if="item.header && !isHidden"
    class="vsm--header"
    :class="item.class"
    v-bind="item.attributes"
  >
    {{ item.header }}
  </li>
  <li
    v-else-if="!isHidden"
    :class="itemClass"
    @mouseover="onMouseOver"
    @mouseout="onMouseOut"
    v-on="(isCollapsed && isFirstLevel) ? { mouseenter: onMouseEnter, mouseleave: onMouseLeave} : {}"
  >
    <component
      :is="linkComponentName ? linkComponentName : 'SidebarMenuLink'"
      :item="item"
      :class="linkClass"
      v-bind="linkAttrs"
      @click="onLinkClick"
    >
      <template v-if="isCollapsed && isFirstLevel">
        <transition name="slide-animation">
          <div
            v-if="hover"
            class="vsm--mobile-bg"
            :style="mobileItemBackgroundStyle"
          />
        </transition>
      </template>
      <sidebar-menu-icon
        v-if="item.icon"
        :icon="item.icon"
      />
      <div
        class="vsm--title"
        :class="(isCollapsed && isFirstLevel) && !isMobileItem && 'vsm--title_hidden'"
        :style="isMobileItem && mobileItemStyle"
      >
        <span>{{ item.title }}</span>
        <sidebar-menu-badge
          v-if="item.badge"
          :badge="item.badge"
        />
        <div
          v-if="hasChild"
          class="vsm--arrow"
          :class="{'vsm--arrow_open' : show}"
        >
          <slot
            name="dropdown-icon"
            v-bind="{ isOpen: show }"
          />
        </div>
      </div>
    </component>
    <template v-if="hasChild">
      <transition
        :appear="isMobileItem"
        name="expand"
        @enter="onExpandEnter"
        @afterEnter="onExpandAfterEnter"
        @beforeLeave="onExpandBeforeLeave"
        @afterLeave="onExpandAfterLeave"
      >
        <div
          v-if="show"
          class="vsm--child"
          :class="isMobileItem && 'vsm--child_mobile'"
          :style="isMobileItem && mobileItemDropdownStyle"
        >
          <ul class="vsm--dropdown">
            <sidebar-menu-item
              v-for="subItem in item.child"
              :key="subItem.id"
              :item="subItem"
              :level="level+1"
            >
              <template #dropdown-icon="{ isOpen }">
                <slot
                  name="dropdown-icon"
                  v-bind="{ isOpen }"
                />
              </template>
            </sidebar-menu-item>
          </ul>
        </div>
      </transition>
    </template>
  </li>
</template>

<script>
import { toRefs } from 'vue'
import { useSidebar } from '../use/useSidebar'
import useItem from '../use/useItem'

import SidebarMenuLink from './SidebarMenuLink.vue'
import SidebarMenuIcon from './SidebarMenuIcon.vue'
import SidebarMenuBadge from './SidebarMenuBadge.vue'

export default {
  name: 'SidebarMenuItem',
  components: {
    SidebarMenuLink,
    SidebarMenuIcon,
    SidebarMenuBadge
  },
  props: {
    item: {
      type: Object,
      required: true
    },
    level: {
      type: Number,
      default: 1
    }
  },
  setup (props) {
    const {
      getSidebarProps,
      getIsCollapsed: isCollapsed
    } = useSidebar()
    const { linkComponentName } = toRefs(getSidebarProps)

    const {
      active,
      exactActive,
      show,
      hover,
      isFirstLevel,
      isHidden,
      hasChild,
      linkClass,
      linkAttrs,
      itemClass,
      isMobileItem,
      mobileItemStyle,
      mobileItemDropdownStyle,
      mobileItemBackgroundStyle,
      onLinkClick,
      onMouseOver,
      onMouseOut,
      onMouseEnter,
      onMouseLeave,
      onExpandEnter,
      onExpandAfterEnter,
      onExpandBeforeLeave,
      onExpandAfterLeave
    } = useItem(props)

    return {
      isCollapsed,
      linkComponentName,
      active,
      exactActive,
      isMobileItem,
      mobileItemStyle,
      mobileItemDropdownStyle,
      mobileItemBackgroundStyle,
      show,
      hover,
      isFirstLevel,
      isHidden,
      hasChild,
      linkClass,
      linkAttrs,
      itemClass,
      onLinkClick,
      onMouseOver,
      onMouseOut,
      onMouseEnter,
      onMouseLeave,
      onExpandEnter,
      onExpandAfterEnter,
      onExpandBeforeLeave,
      onExpandAfterLeave
    }
  }
}
</script>
