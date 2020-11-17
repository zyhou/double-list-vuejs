<template>
  <div class="container">
    <div class="wrapper">
      <section class="left">
        <div class="wrapper" v-for="card in leftCards" :key="card.id">
          <article
            :class="{ active: card.id === currentCardId }"
            :style="{ backgroundColor: card.color }"
            @click="onSelectedCard(card.id)"
          >
            <input type="text" v-model="card.color" placeholder="color name" />
          </article>
          <button @click="deleteCard(card.id)">x</button>
        </div>
      </section>
      <button @click="addCard('left')">+</button>
    </div>

    <section class="middle">
      <button @click="moveSideSelectedCard()">Move</button>
      <button @click="deleteSelectedCard()">Delete</button>
      <button @click="copySelectedCard()">Copy</button>
      <button>Reference</button>
    </section>

    <div class="wrapper">
      <section class="right">
        <div class="wrapper" v-for="card in rightCards" :key="card.id">
          <article
            :class="{ active: card.id === currentCardId }"
            :style="{ backgroundColor: card.color }"
            @click="onSelectedCard(card.id)"
          >
            <input type="text" v-model="card.color" placeholder="color name" />
          </article>
          <button @click="deleteCard(card.id)">x</button>
        </div>
      </section>
      <button @click="addCard('right')">+</button>
    </div>
  </div>
</template>

<script>
import { ref, computed } from "vue";
import { v4 as uuidv4 } from "uuid";

export default {
  name: "App",
  setup() {
    const cards = ref([
      { id: uuidv4(), color: "blue", side: "left" },
      { id: uuidv4(), color: "orange", side: "left" },
      { id: uuidv4(), color: "green", side: "left" },
      { id: uuidv4(), color: "black", side: "left" },
      { id: uuidv4(), color: "grey", side: "right" },
    ]);

    const currentCardId = ref(null);

    const leftCards = computed(() =>
      cards.value.filter((c) => c.side === "left")
    );

    const rightCards = computed(() =>
      cards.value.filter((c) => c.side === "right")
    );

    const addCard = (side) => {
      cards.value = [...cards.value, { id: uuidv4(), color: "black", side }];
    };

    const deleteCard = (cardId) => {
      cards.value = cards.value.filter((c) => c.id !== cardId);
    };

    const onSelectedCard = (cardId) => {
      currentCardId.value = currentCardId.value === cardId ? null : cardId;
    };

    const deleteSelectedCard = () => {
      if (currentCardId.value === null) {
        return;
      }

      cards.value = cards.value.filter((c) => c.id !== currentCardId.value);
    };

    const moveToSide = (items, currentItemId, side) => {
      const currentCardIndex = items.findIndex((c) => c.id === currentItemId);
      const currentCard = items[currentCardIndex];
      return [
        ...items.filter((c) => c.id !== currentItemId),
        { ...currentCard, side },
      ];
    };

    const moveSideSelectedCard = () => {
      if (currentCardId.value === null) {
        return;
      }

      const isPresentOnRightSide =
        rightCards.value.findIndex((c) => c.id === currentCardId.value) !== -1;
      if (isPresentOnRightSide) {
        cards.value = moveToSide(cards.value, currentCardId.value, "left");
        return;
      }

      const isPresentOnLeftSide =
        leftCards.value.findIndex((c) => c.id === currentCardId.value) !== -1;
      if (isPresentOnLeftSide) {
        cards.value = moveToSide(cards.value, currentCardId.value, "right");
      }
    };

    const copySelectedCard = () => {
      if (currentCardId.value === null) {
        return;
      }

      const currentCard = cards.value.find((c) => c.id === currentCardId.value);
      cards.value = [...cards.value, { ...currentCard, id: uuidv4() }];
    };

    return {
      leftCards,
      rightCards,
      deleteCard,
      addCard,
      onSelectedCard,
      deleteSelectedCard,
      currentCardId,
      moveSideSelectedCard,
      copySelectedCard,
    };
  },
};
</script>
