# shopify-fillter-tab-for-collection
SHOPIFY FILLTERATION TAB FOR PRODUCTS



<!-- tabs-carousel.liquid -->

<div
  class="tabs-carousel-wrapper page-width"
  style="color: {{ section.settings.text_color }}; font-family: {{ section.settings.font_family }}; background-color: {{ section.settings.section_background_color }};"
>
  <!-- Tabs -->
  <div class="tabs">
    <button class="tab-link active" data-collection="{{ section.settings.tab1_collection }}">
      {{ section.settings.tab1_title }}
    </button>
    <button class="tab-link" data-collection="{{ section.settings.tab2_collection }}">
      {{ section.settings.tab2_title }}
    </button>
    <button class="tab-link" data-collection="{{ section.settings.tab3_collection }}">
      {{ section.settings.tab3_title }}
    </button>
  </div>

  <!-- Product Carousel -->
  <div class="product-carousel">
    <div class="carousel-items">
      <!-- Products will be loaded here based on selected tab -->
    </div>
    <div class="arrow-btn">
      <div class="arrow-left" disabled>←</div>
      <div class="arrow-right">→</div>
    </div>
  </div>
</div>

<style>
  .tabs-carousel-wrapper {
    padding: 20px;
  }
  .tabs {
    display: flex;
    justify-content: center;
    gap: 30px;
    margin-bottom: 20px;
  }
  .tab-link {
    padding: 10px 20px;
    border: none;
    background-color: {{ section.settings.tab_background_color }};
    cursor: pointer;
    border: 1px solid #000;
  }
  .tab-link.active {
    background-color: {{ section.settings.active_tab_color }};
    color: white;
  }
  .product-carousel {
    position: relative;
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    overflow: hidden;
  }
  .carousel-items {
    display: flex;
    transition: transform 0.5s ease; /* Smooth transition */
    width: 100%;
  }
  .carousel-items .product {
    flex: 0 0 25%; /* 4 products in one view */
    padding: 10px;
  }
  .arrow-btn {
    position: relative;
    width: 100%;
    display: flex;
    justify-content: center;
    gap: 20px;
  }
  .arrow-left, .arrow-right {
    cursor: pointer;
    background-color: #007bff;
    color: white;
    padding: 10px;
    z-index: 10;
  }
  .product img {
    width: 100%;
  }
</style>

<script>
document.addEventListener('DOMContentLoaded', function () {
  const tabLinks = document.querySelectorAll('.tab-link');
  const carouselItems = document.querySelector('.carousel-items');

  // Tab Click Event
  tabLinks.forEach((tab) => {
    tab.addEventListener('click', function () {
      document.querySelector('.tab-link.active').classList.remove('active');
      this.classList.add('active');

      const collectionHandle = this.dataset.collection;
      loadCollectionProducts(collectionHandle);
    });
  });

  // Initial Load for First Tab
  loadCollectionProducts('{{ section.settings.tab1_collection }}');

  function loadCollectionProducts(collectionHandle) {
    fetch(`/collections/${collectionHandle}/products.json`)
      .then((response) => response.json())
      .then((data) => {
        carouselItems.innerHTML = '';
        data.products.forEach((product) => {
          carouselItems.innerHTML += `
            <div class="product">
              <img src="${product.images[0].src}" alt="${product.title}" />
              <h3>${product.title}</h3>
              <p>${product.variants[0].price}</p>
            </div>`;
        });
        updateArrowState(); // Update arrow state after loading products
      });
  }

  // Carousel Navigation
  const arrowLeft = document.querySelector('.arrow-left');
  const arrowRight = document.querySelector('.arrow-right');
  let scrollPosition = 0;

  arrowRight.addEventListener('click', function () {
    const items = carouselItems.querySelectorAll('.product');
    const itemWidth = items[0].offsetWidth;

    if (scrollPosition + itemWidth < items.length * itemWidth) {
      scrollPosition += itemWidth;
      carouselItems.style.transform = `translateX(-${scrollPosition}px)`;
      updateArrowState();
    } else {
      // Reset to first product
      scrollPosition = 0;
      carouselItems.style.transform = `translateX(0)`;
      updateArrowState();
    }
  });

  arrowLeft.addEventListener('click', function () {
    const items = carouselItems.querySelectorAll('.product');
    const itemWidth = items[0].offsetWidth;

    if (scrollPosition - itemWidth >= 0) {
      scrollPosition -= itemWidth;
      carouselItems.style.transform = `translateX(-${scrollPosition}px)`;
      updateArrowState();
    } else {
      // Do nothing if already at the start
    }
  });

  function updateArrowState() {
    const items = carouselItems.querySelectorAll('.product');
    const itemWidth = items[0].offsetWidth;

    arrowLeft.disabled = (scrollPosition === 0);
    arrowRight.disabled = (scrollPosition >= items.length * itemWidth - itemWidth);
  }
});
</script>

{% schema %}
{
  "name": "Tabs Carousel",
  "settings": [
    {
      "type": "text",
      "id": "tab1_title",
      "label": "Tab 1 Title",
      "default": "Collection 1"
    },
    {
      "type": "collection",
      "id": "tab1_collection",
      "label": "Tab 1 Collection"
    },
    {
      "type": "text",
      "id": "tab2_title",
      "label": "Tab 2 Title",
      "default": "Collection 2"
    },
    {
      "type": "collection",
      "id": "tab2_collection",
      "label": "Tab 2 Collection"
    },
    {
      "type": "text",
      "id": "tab3_title",
      "label": "Tab 3 Title",
      "default": "Collection 3"
    },
    {
      "type": "collection",
      "id": "tab3_collection",
      "label": "Tab 3 Collection"
    },
    {
      "type": "color",
      "id": "section_background_color",
      "label": "Section Background Color",
      "default": "#ffffff"
    },
    {
      "type": "color",
      "id": "text_color",
      "label": "Text Color",
      "default": "#000000"
    },
    {
      "type": "color",
      "id": "tab_background_color",
      "label": "Tab Background Color",
      "default": "#f1f1f1"
    },
    {
      "type": "color",
      "id": "active_tab_color",
      "label": "Active Tab Color",
      "default": "#007bff"
    },
    {
      "type": "select",
      "id": "font_family",
      "label": "Font Family",
      "options": [
        {
          "value": "Helvetica, sans-serif",
          "label": "Helvetica"
        },
        {
          "value": "'Times New Roman', Times, serif",
          "label": "Times New Roman"
        },
        {
          "value": "'Arial', sans-serif",
          "label": "Arial"
        },
        {
          "value": "'Courier New', Courier, monospace",
          "label": "Courier New"
        },
        {
          "value": "'Georgia', serif",
          "label": "Georgia"
        }
      ],
      "default": "Helvetica, sans-serif"
    },
    {
      "type": "select",
      "id": "color_scheme",
      "label": "Color Scheme",
      "options": [
        {
          "value": "light",
          "label": "Light"
        },
        {
          "value": "dark",
          "label": "Dark"
        }
      ],
      "default": "light"
    }
  ],
  "presets": [
    {
      "name": "Default",
      "category": "Custom"
    }
  ]
}
{% endschema %}
