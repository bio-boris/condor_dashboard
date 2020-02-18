<template>
  <div>
    partitionableslot = {{mslot.classad.partitionableslot}}
    CHILD MEM {{mslot.classad.childmemory}}
    CHILD CPU {{mslot.classad.childcpus}}
    <br />
    TOTAL MEM {{mslot.classad.totalmemory}}
    TOTAL SLOT MEM {{mslot.classad.totalslotmemory}}
    DETECTED MEM {{mslot.classad.detectedmemory}}
    DETECTED VM {{mslot.classad.virtualmemory}}
    DETECTED TVM {{mslot.classad.totalvirtualmemory}}
    <br />
    totalslotcpus {{mslot.classad.totalslotcpus}}
    detectedcpus {{mslot.classad.detectedcpus}}
    cpubusytime {{mslot.classad.cpubusytime}}
    Disk {{mslot.classad.diskUsage}}
    <br />
    NumDynamicSlots {{mslot.classad.numdynamicslots}}
    RecentJobStarts {{mslot.classad.recentjobstarts}}
    <br />
    TotalCpus {{mslot.classad.totalcpus}}
    TotalDisk {{mslot.classad.totaldisk}}
    TotalLoadAvg {{mslot.classad.totalloadavg}}
    TotalMemory {{mslot.classad.totalmemory}}
    TotalVirtualMemory {{mslot.classad.totalvirtualmemory}}
    <br />

    <!-- mon_self_usg {{mslot.classad.monitorselfcpuusage}} -->
    <hr />
  </div>
</template>

<script>
export default {
  name: "MachineSlot",
  props: ["mslot"],
  data: function() {
    return {
      id: null
    };
  }
};
</script>




<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>