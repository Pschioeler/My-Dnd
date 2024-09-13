<template>
  <div class="battle-tracker">
    <div class="roundTracker">
      <p>Round: {{ round }}</p>
    </div>
    <div class="turnTracker">
      <h3 v-if="currentCombatant">Turn: {{ currentCombatant.name }}</h3>
      <h3 v-else>Turn:</h3>
    </div>
    <div class="controls">
      <button class="buttonStyle-1" @click="nextTurn">Next</button>
      <button class="buttonStyle-1" @click="reset">Reset</button>
      <button class="buttonStyle-1" @click="clear">Clear</button>
      <button class="buttonStyle-1" @click="AddCombatant">Add Combatant</button>
    </div>
    <div v-if="combatants.length" class="combatants-section">
      <ul>
        <li v-for="(combatant, index) in combatants" :key="index" class="combatant" :class="{ active: turn === index }">
          <div class="removeBox">
            <button @click="removeCombatant(index)" class="removeButton">Remove</button>
          </div>
          <div class="nameBox">
            <input type="text" v-model="combatant.name"/>
          </div>
          <div class="initiativeBox">
            <label class="labelStyle-1" for="Initiative">Initiative</label>
            <input type="number" v-model.number="combatant.initiative" @blur="sortCombatants" @keyup.enter="sortCombatants"/>
          </div>
          <div class="acBox">
            <label class="labelStyle-1" for="AC">AC</label>
            <input type="number" v-model.number="combatant.ac" />
          </div>
          <div class="healthSummaryBox">
            <div class="HSB-tempHealth">
              <label class="labelStyle-1" for="tempHealth">Temp</label>
              <input type="number" v-model.number="combatant.tempHealth" />
            </div>
            <div class="HSB-showHealth">
              <div class="HSB-showHealth-current">
                <label class="labelStyle-1" for="currentHealth">Current</label>
                <input type="number" v-model.number="combatant.currentHealth" />
              </div>
              <div class="HSB-showHealth-slash large">
                /
              </div>
              <div class="HSB-showHealth-max">
                <label class="labelStyle-1" for="currentHealth">Max</label>
                <input type="number" v-model.number="combatant.totalHealth" />
              </div>
            </div>
            <div class="HSB-adjustHealth">
              <button @click="healCombatant(index)" class="healButton">Heal</button>
              <input type="number" v-model.number="combatant.adjustValue"/>
              <button @click="damageCombatant(index)" class="damageButton">Damage</button>
            </div>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      round: 0,
      turn: 0,
      combatants: [],
      newCombatant: {
        name: '',
        initiative: 0,
        ac: 0,
        totalHealth: 0,
        currentHealth: 0,
        tempHealth: 0,
        adjustValue: 0
      }
    };
  },
  computed: {
    currentCombatant() {
      return this.combatants[this.turn] || null;
    }
  },
  methods: {
    nextTurn() {
      if (this.turn >= this.combatants.length - 1) {
        this.round++;
        this.turn = 0;
      } else {
        this.turn++;
      }
    },
    reset() {
      this.round = 0;
      this.turn = 0;
      this.combatants.forEach(combatant => combatant.initiative = 0);
    },
    clear() {
      this.round = 0;
      this.turn = 0;
      this.combatants = [];
    },
    AddCombatant() {
      this.combatants.push({ ...this.newCombatant, name: "", initiative: 0, ac: 0, totalHealth: 0, currentHealth: 0, tempHealth: 0, adjustValue: 0 });
    },
    removeCombatant(index) {
      this.combatants.splice(index, 1);
      if (this.turn >= this.combatants.length) {
        this.turn = this.combatants.length - 1;
      }
    },
    healCombatant(index) {
      const value = parseInt(this.combatants[index].adjustValue, 10);
      if (!isNaN(value)) {
        this.combatants[index].currentHealth += value;
        if (this.combatants[index].currentHealth > this.combatants[index].totalHealth) {
          this.combatants[index].currentHealth = this.combatants[index].totalHealth;
        }
      }
      this.combatants[index].adjustValue = 0;
    },
    damageCombatant(index) {
      const value = parseInt(this.combatants[index].adjustValue, 10);
      if (!isNaN(value)) {
        this.combatants[index].currentHealth -= value;
        if (this.combatants[index].currentHealth < 0) {
          this.combatants[index].currentHealth = 0;
        }
      }
      this.combatants[index].adjustValue = 0;
    },
    sortCombatants() {
      this.combatants.sort((a, b) => b.initiative - a.initiative);
    }
  }
};
</script>

<style scoped>
/* Chrome, Safari, Edge, Opera */
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

/* Firefox */
input[type=number] {
  -moz-appearance: textfield;
}

.battle-tracker {
  border: 2px solid var(--primary);
  padding: 20px;
  border-radius: 10px;
  width: 80%;
  margin: 20px auto;
  background-color: var(--lightbrown);
}

.controls {
  text-align: center;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 20px 0;
}

.roundTracker {
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 10px 0;
  font-weight: 700;
  text-transform: uppercase;
  font-size: 1.5em;
}

.turnTracker {
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 10px 0;
  font-weight: 700;
  text-transform: uppercase;
  font-size: 1em;
}

.combatants-section {
  border-top: 2px solid var(--primary);;
  margin-top: 20px;
  padding-top: 20px;
}

.combatant {
  border: 2px solid #822000;
  border-radius: 5px;
  padding: 10px;
  margin-bottom: 30px;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  width: 100%;
}

.combatant.active {
  background-color: var(--active);
}

.combatant > div {
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 10px;
}

.healButton {
  width: 100px;
  color: var(--green-1);
  align-items: center;
  border: 2px solid;
  border-radius: 5px;
  cursor: pointer;
  font-weight: 700;
  padding: 5px 13px 4px;
  text-transform: uppercase;
  white-space: nowrap;
  background-color: var(--bg);
  transition: background-color 0.3s ease;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
}

.damageButton {
  width: 100px;
  color: var(--red-1);
  align-items: center;
  border: 2px solid;
  border-radius: 5px;
  cursor: pointer;
  font-weight: 700;
  padding: 5px 13px 4px;
  text-transform: uppercase;
  white-space: nowrap;
  background-color: var(--bg);
  transition: background-color 0.3s ease;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
}

.healButton:hover, 
.damageButton:hover {
  background-color: var(--primary-fade);
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.3);
}

.removeButton{
  background: none;
  border: 0;
  box-sizing: border-box;
  color: transparent;
  cursor: pointer;
  font-size: 18px;
  letter-spacing: 1.5px;
  line-height: 90px;
  outline: none;
  overflow: hidden;
  text-transform: uppercase;
  transform: translate(-0%, -0%);
  transition: all 0.2s ease-in;
}

.removeButton::before,
.removeButton::after {
  background-color: var(--red-1);
  content: '';
  display: block;
  height: 3px;
  left: 0;
  position: absolute;
  transform-origin: center left;
  transition: all 0.2s ease-in;
  width: 141.4214px;
  z-index: -1;
}

.removeButton::before {
  top: 0;
  transform: rotate(45deg);
}

.removeButton::after {
  bottom: 0;
  transform: rotate(-45deg);
}

.removeButton:hover {
  color: var(--bg);
}

.removeButton:hover::before,
.removeButton:hover::after {
  height: 50px;
  transform: rotate(0deg);
}

.nameBox {
  border: 2px solid;
  width: 200px;
}

.initiativeBox {
  flex-direction: column;
  border: 2px solid;
  width: 120px;
}

.acBox {
  flex-direction: column;
  border: 2px solid;
  width: 120px;
}

.healthSummaryBox {
  display: flex;
  flex-direction: row;
  border: 2px solid;
}

.healthSummaryBox > div {
  margin: 0 10px;
  padding: 0 5px;
}

.HSB-tempHealth, .HSB-adjustHealth{
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.HSB-showHealth {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
}

.HSB-showHealth > div {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.removeBox {
  border: 2px solid;
}

input[type="number"] {
  width: 100px;
  text-align: center;
  background-color: var(--bg);
  color: var(--secondary);
  border: 2px solid;
  font-weight: 700;
  margin: 10px;
  padding: 5px 13px 4px;
  cursor: pointer;
  border-radius: 5px;
  transition: background-color 0.3s ease;
}

input[type="name"], input[type="text"] {
  width: 100%;
  text-align: center;
  background-color: var(--bg);
  color: var(--secondary);
  border: 2px solid;
  font-weight: 700;
  padding: 5px 13px 4px;
  cursor: pointer;
  border-radius: 5px;
  transition: background-color 0.3s ease;
}

.buttonStyle-1 {
  align-items: center;
  border: 2px solid var(--primary);
  border-radius: 3px;
  cursor: pointer;
  display: flex;
  font-weight: 700;
  margin-right: 20px;
  padding: 5px 13px 4px;
  text-transform: uppercase;
  white-space: nowrap;
  background-color: var(--bg);
  transition: background-color 0.3s ease;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
}

.buttonStyle-1:hover, input[type="number"]:hover, input[type="text"]:hover {
  background-color: var(--primary-hover); 
}

.labelStyle-1, .HSB-showHealth-slash {
  font-weight: 700;
  text-transform: uppercase;
  font-size: 1em;
}

.large {
  font-size: 2.5em;
  padding-top: 20px;
}

</style>
