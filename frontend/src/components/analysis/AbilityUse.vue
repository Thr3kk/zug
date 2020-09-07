<template>
  <div id="ability-use">
    <md-toolbar class="md-dense">
      <h3 class="md-title">Ability Use</h3>
    </md-toolbar>
    <div class="analysis-block">
      <template v-if="downrankers">
        <h3>Higher rank abilities are available for training!</h3>
        <div class="md-layout md-row"> 
          <div v-for="(abilities, playerID, i) in downrankers" v-bind:key="playerID" class="playerDownrank md-layout-item md-small-size-100">
            <p><strong :class="report.raid[playerID].type.toLowerCase()">{{report.raid[playerID].name}}</strong></p>
            <ul>
              <li v-for="(k, spell) in abilities" v-bind:key="i+'-'+spell" ><a :href="`https://classic.wowhead.com/spell=${spell}`">{{spell}}</a></li>
            </ul>
          </div>
        </div>
      </template>
      <p><small>* Currently, only warrior abilities are looked at.</small></p>
    </div>
  </div>
</template>

<script>

const lowRanks = {
  // --- WARRIOR
  // heroic strike
  78: 25286,
  284: 25286,
  285: 25286,
  1608: 25286,
  11564: 25286,
  11565: 25286,
  11566: 25286,
  11567: 25286,
  // battle shout
  6673: 25289,
  5242: 25289,
  6192: 25289,
  11549: 25289,
  11550: 25289,
  11551: 25289,
  // charge
  100: 11578,
  6178: 11578,
  // rend (but why?)
  772: 11574,
  6546: 11574,
  6547: 11574,
  6548: 11574,
  11572: 11574,
  11573: 11574,
  // thunderclap
  6343: 11581,
  8198: 11581,
  8204: 11581,
  8205: 11581,
  11580: 11581,
  // hamstring
  1715: 7373,
  7372: 7373,
  // sunder armor
  7386: 11597,
  7405: 11597,
  8380: 11597,
  11596: 11597,
  // overpower
  7384: 11585,
  7887: 11585,
  11584: 11585,
  // shield bash
  72: 1672,
  1671: 1672,
  // demo shout
  1160: 11556,
  6190: 11556,
  11554: 11556,
  11555: 11556,
  // revenge
  6572: 25288,
  6574: 25288,
  7379: 25288,
  11600: 25288,
  11601: 25288,
  // mocking blow
  694: 20560,
  7400: 20560,
  7402: 20560,
  20559: 20560,
  // cleave
  845: 20569,
  7369: 20569,
  11608: 20569,
  11609: 20569,
  // execute
  5308: 20662,
  20658: 20662,
  20660: 20662,
  20661: 20662,
  // intecept
  20252: 20617,
  20616: 20617,
  // slam
  1464: 11605,
  8820: 11605,
  11604: 11605,
  // pummel
  6552: 6554,
  // mortal strike
  12294: 21553,
  21551: 21553,
  21552: 21553,
  // bloodthirst
  23881: 23894,
  23892: 23894,
  23893: 23894,
  // shield slam
  23922: 23925,
  23923: 23925,
  23924: 23925
  
    // --- ROGUE
  // Sinister Strike
  1752: 11294,
  1757: 11294,
  1758: 11294,
  1759: 11294,
  1760: 11294,
  8621: 11294,
  11293: 11294,
  // Backstab
  53: 25300,
  2589: 25300,
  2590: 25300,
  2591: 25300,
  8721: 25300,
  11279: 25300,
  11280: 25300,
  11281: 25300,
  //Hemorrhage
  16511: 17348,
  17347: 17348,  
  //Gouge
  1776: 11286,
  1777: 11286,
  8629: 11286,
  11285: 11286,  
  // Kick
  1766: 1769,
  1767: 1769,
  1768: 1769,
  // Slice and Dice
  5171: 6774,
  // Eviscerate (P6-Rank9 skill commented out)
  2098: 11300,
  6760: 11300,
  6761: 11300,
  6762: 11300,
  8623: 11300,
  8624: 11300,
  11299: 11300,
  //  2098: 31016,
  //  6760: 31016,
  //  6761: 31016,
  //  6762: 31016,
  //  8623: 31016,
  //  8624: 31016,
  //  11299: 31016,
  //  11300: 31016,
  // Expose Armor
  8647: 11198,
  8649: 11198,
  8650: 11198,
  11197: 11198,
  // Rupture
  1943: 11275,
  8639: 11275,
  8640: 11275,
  11273: 11275,
  11274: 11275,
  // Kidney Shot
  408: 8643,
  // Ambush
  8678: 11269,
  8724: 11269,
  8725: 11269,
  11267: 11269,
  11268: 11269,
  // Sap
  6770: 11297,
  2070: 11297,
  //  Garrote
  703: 11290,
  8631: 11290,
  8632: 11290,
  8633: 11290,
  11289: 11290,
  // Stealth
  1784: 1787,
  1785: 1787,
  1786: 1787,
  // Vanish
  1856:  1857,
  // Feint
  1966: 25302,
  6768: 25302,
  8637: 25302,
  11303: 25302,
  // Sprint
  2983: 11305,
  8696: 11305,
  // Instant Poison
  8681: 11343,
  6949: 11343,
  6950: 11343,
  8926: 11343,
  11342: 11343,
  // Deadly Poison
  2892: 20844,
  2837: 20844,
  11357: 20844,
  11358: 20844,
  // Crippling Poison
  3775: 3776,
  // Mind-numbing Poison
  5763: 9186,
  6951: 9186,
  
}

export default {
  props: {
    report: Object,
    fightKey: Number
  },
  mounted: async function () {
    this.process()
    Object.keys(this.$options.props).forEach(key => {
      this.$watch(key, this.process)
    })
  },
  data: function () {
    return {
      downrankers: null
    }
  },
  methods: {
    process: function () {
      var downranked = {}
      for (let cast of this.report.fights[this.fightKey].casts) {
        if (lowRanks[cast.ability.guid]) {
          downranked[cast.sourceID] = downranked[cast.sourceID] || {}
          downranked[cast.sourceID][cast.ability.guid] = 1
        }
      }
      this.downrankers = downranked
      this.$nextTick(() => {
        window.$WowheadPower.refreshLinks()
      })
    },
    getPlayerName: function (playerID) {
      if (this.report.raid[playerID]) return this.report.raid[playerID].name
      return 'Unknown'
    },
    getPlayerClass: function (playerID) {
      if (this.report.raid[playerID]) return this.report.raid[playerID].type.toLowerCase()
      return 'Unknown'
    }
  }
}
</script>

<style scoped lang="scss">
.analysis-block {
  min-height: 250px;
  border: 1px solid var(--md-theme-default-toolbarvariant, #212121);
  border-radius: 0 0 4px 4px;
  border-top: 0;
  padding: 20px;
}
.playerDownrank {
  padding: 10px 10px 0;
  background: #383838;
  border-radius: 4px;
  margin: 10px;
  @media (min-width: 1200px) {
    min-width: 211px;
    max-width: 211px;
    width: 211px;
  }
}
.playerDownrank ul {
  padding: 0;
  list-style-type: none;
}
.playerDownrank p {
  margin: 0;
}
</style>
