<template>
  <div class="Graph" id="network"></div>
</template>

<script>
import { Network } from "vis-network/peer/esm/vis-network";
import { DataSet } from "vis-data/peer";
export default {
  name: "Network",
  methods: {
    draw() {
      var EDGE_LENGTH_MAIN = 150;
      var EDGE_LENGTH_SUB = 50;
      const DIR = "/img/network/"

      let nodes = [];
      let edges = [];

      const internet = {
        id: 1,
        label: "Internet",
        image: DIR + "internet.png",
        shape: "image"
      };

      const router = {
        id: 2,
        label: "Router",
        image: DIR + "router.png",
        shape: "image"
      };

      edges.push({ id: 1001, from: 1, to: 2, length: EDGE_LENGTH_MAIN });

      const firewalls = [
        {
          id: 3,
          label: "Firewall",
          image: DIR + "firewall.png",
          shape: "image"
        },
        {
          id: 5,
          label: "Firewall",
          image: DIR + "firewall.png",
          shape: "image"
        }
      ];

      edges.push({ from: 2, to: 3, length: EDGE_LENGTH_MAIN });

      const swtch = {
        id: 4,
        label: "Switch",
        image: DIR + "switch.png",
        shape: "image"
      }

      edges.push({ from: 3, to: 4, length: EDGE_LENGTH_MAIN });
      edges.push({ from: 4, to: 5, length: EDGE_LENGTH_MAIN });

      const vlan1 = [];
      for (let i = 6; i < 9; i++) {
        vlan1.push({
          id: i,
          label: "Computer",
          image: DIR + "computer.png",
          shape: "image"
        })
        edges.push({ from: 4, to: i, length: EDGE_LENGTH_MAIN });
      }

      const vlan2 = [
        {
          id: 9,
          label: "Mail Server",
          image: DIR + "mail-server.png",
          shape: "image"
        },
        {
          id: 10,
          label: "Web Server",
          image: DIR + "web-server.png",
          shape: "image"
        }
      ];
      edges.push({ from: 5, to: 9, length: EDGE_LENGTH_MAIN });
      edges.push({ from: 5, to: 10, length: EDGE_LENGTH_MAIN });

      const vlan3 = [
        {
          id: 11,
          label: "Wifi",
          image: DIR + "wifi.png",
          shape: "image"
        }
      ];
      edges.push({ from: 4, to: 11, length: EDGE_LENGTH_MAIN });

      const smartphones = [];
      for (let i = 100; i < 103; i++) {
        smartphones.push({
          id: i,
          label: "Smartphone",
          image: DIR + "smartphone.png",
          shape: "image"
        })
        edges.push({ from: 11, to: i, length: EDGE_LENGTH_MAIN });
      }
      vlan3.push(...smartphones);


      const vlan4 = [
        {
          id: 12,
          label: "Database Server",
          image: DIR + "database-server.png",
          shape: "image"
        }
      ];
      edges.push({ from: 4, to: 12, length: EDGE_LENGTH_MAIN });

      const vlan5 = [
        {
          id: 13,
          label: "Backup Server",
          image: DIR + "database-server.png",
          shape: "image"
        }
      ];
      edges.push({ from: 4, to: 13, length: EDGE_LENGTH_MAIN });

      nodes.push(internet);
      nodes.push(router);
      nodes.push(...firewalls);
      nodes.push(swtch);
      nodes.push(...vlan1);
      nodes.push(...vlan2);
      nodes.push(...vlan3);
      nodes.push(...vlan4);
      nodes.push(...vlan5);

      // create a network
      var container = document.getElementById("network");
      nodes = new DataSet(nodes);
      edges = new DataSet(edges);
      var data = {
        nodes: nodes,
        edges: edges
      };
      var options = {
        nodes: {
          font: {
            color: "white"
          }
        }
      };
      let network = new Network(container, data, options);

      setTimeout(_ => {
        edges.update({id: 1001, color: {color: 'red'}})
      }, 5000)
    }
  },
  mounted() {
    this.draw();
  }
};
</script>

<style scoped>
.Graph {
    height: 100%;
}
</style>
