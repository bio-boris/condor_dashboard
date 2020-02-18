<template>
  <div v-bind:name="machine.name">
    <b>{{ machine.name }}</b>
    Number of jobs {{ machine.slots.length - 1 }}
    <span>
      Memory {{ Math.floor(machine.classad.detectedmemory / 1024) }}GB
      CPUS {{ machine.classad.detectedcpus }}
    </span>
    <hr />
    <!-- {{ machine.slots }} -->
    <!-- <MachineSlot v-bind:slot="slot"></MachineSlot> -->

    <div v-for="mslot in machine.slots" v-bind:key="mslot.uniq">
      <MachineSlot v-bind:mslot="mslot"></MachineSlot>
    </div>
  </div>
</template>

<script>
import MachineSlot from "./MachineSlot.vue";

// export default {
//   name: "MachineSlot"
// };

export default {
  name: "Machine",
  props: ["machine"],
  data: function() {
    return {
      // machine: null,
      slots: this.machine.slots
    };
  },
  components: {
    MachineSlot
  }
};
</script>
<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>