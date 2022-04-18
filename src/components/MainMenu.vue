<script lang="ts">
import { defineComponent, ref, Ref } from "vue";
import { Menu, MenuItemModel, MenuItemArrow } from "@progress/kendo-vue-layout";

interface Props {
  isMobile: Ref<boolean>;

  navs: MenuItemModel[] | undefined;
}

export interface MenuModel {
  Text: string;

  Route?: string | undefined;

  Icon?: string | undefined;

  Items?: MenuModel[] | null;
}

/** Returns all the menu items */
export function getKnownRoutes(): MenuModel[] {
  const menuItems = [
    {
      Text: "Home",
      Route: "/home",
      Icon: "home",
    } as MenuModel,
    {
      Text: "About",
      Route: "/about",
      Icon: "info",
    } as MenuModel,
    {
      Text: "SubMenu",
      Icon: "menu",
      Items: [
        {
          Text: "Item 1",
          Route: "/item1",
          Icon: "Heart",
        },
        {
          Text: "Item 2",
          Route: "/item2",
          Icon: "Heart",
        },
      ],
    } as MenuModel,
  ];

  return menuItems;
}

/** Converts MenuModel into MenuItemModel which a Kendo Menu Component can read. */
export function convertMenuModelIntoMenuItemModel(
  model: MenuModel
): MenuItemModel {
  return {
    text: model.Text,
    url: model.Route,
    icon: model.Icon,
    items: model.Items?.map((el) => convertMenuModelIntoMenuItemModel(el)),
  } as MenuItemModel;
}

/** Add a linkRender property in MenuItemModel */
export function addRenderLink(model: MenuItemModel): MenuItemModel {
  model.linkRender = "linkRender";
  return model;
}

/** Add SubMenu Title if Parent has Items. */
export function addSubMenuTitle(model: MenuItemModel): MenuItemModel {
  if (model.items && model.items.length) {
    model.items.unshift({ text: model.text, disabled: true }) as MenuItemModel;
  }
  return model;
}

export default defineComponent({
  name: "TheMainMenu",
  components: {
    "k-menu": Menu,
    // MenuItemArrow,
  },
  setup() {
    // get the menuItems
    const navs = getKnownRoutes().map((el) =>
      convertMenuModelIntoMenuItemModel(el)
    );

    // Add RenderLink to the first level items.
    // Add Submenu title to the first level items.
    navs.forEach((el) => addRenderLink(el));
    navs.forEach((el) => addSubMenuTitle(el));

    const isMobile = ref(true);
    return {
      navs,
      isMobile,
    } as Props;
  },
  methods: {
    toggleNavMenu() {
      this.isMobile = !this.isMobile;
    },
  },
});
</script>

<template>
  <link
    rel="stylesheet"
    href="https://dashkit.goodthemes.co/assets/css/theme.bundle.css"
  />
  <nav
    class="navbar navbar-vertical navbar-vertical-sm fixed-start navbar-expand-md navbar-dark"
  >
    <!-- NavMenu Body -->
    <div class="container-fluid">
      <!-- Navbar Toggle Button -->
      <button
        class="navbar-toggler"
        type="button"
        @click="toggleNavMenu"
        data-toggle="collapse"
        data-target="#navbarSupportedContent"
        aria-controls="navbarSupportedContent"
        aria-expanded="false"
        aria-label="Toggle navigation"
      >
        <span class="navbar-toggler-icon"></span>
      </button>

      <!-- Navbar Brand -->
      <a class="navbar-brand" href="#">N</a>
      <div class="collapse navbar-collapse show" v-if="isMobile">
        <!-- Divider -->
        <hr class="navbar-divider d-none d-md-block mt-0 mb-3" />
        <!--Menu Items -->
        <ul class="navbar-nav">
          <k-menu :items="navs" :vertical="true">
            <template v-slot:linkRender="{ props }">
              <div style="text-align: center" class="my-3">
                <span
                  :class="`k-icon k-i-${props.item.icon}`"
                  class="mx-2"
                  style="color: white"
                >
                </span>
                <span class="d-md-none" style="color:white">{{ props.item.text }}</span>
                <!-- <MenuItemArrow
                v-if="props.item.items && props.item.items.length > 0"
                :itemId="props.itemId"
                :vertical-menu="false"
                :dir="props.dir"
                key="1"
              /> -->
              </div>
            </template>
          </k-menu>
        </ul>
      </div>
    </div>
  </nav>
</template>
