<template>
  <div class="Graph" id="network"></div>
</template>

<script>
import { Network } from "vis-network/peer/esm/vis-network";
import { DataSet } from "vis-data/peer";
export default {
  name: "Network",
  data() {
    return {
      nodes: [],
      edges: [],
      edgesActivity: [],
      network: {},
      endDevices: [],
      startDevices: [],
      nodesList: [],
      edgesList: []
    };
  },
  methods: {
    draw() {
      var EDGE_LENGTH_MAIN = 150;
      var EDGE_LENGTH_SUB = 50;
      const DIR = "/img/network/";

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
      this.startDevices.push(internet);
      edges.push({
        id: 1000,
        from: 1,
        to: 2,
        length: EDGE_LENGTH_MAIN,
        color: { color: "#6bc1ff" }
      });

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

      edges.push({
        id: 1009,
        from: 2,
        to: 3,
        length: EDGE_LENGTH_MAIN,
        color: { color: "#6bc1ff" }
      });

      const swtch = {
        id: 4,
        label: "Switch",
        image: DIR + "switch.png",
        shape: "image"
      };

      edges.push({
        id: 1003,
        from: 3,
        to: 4,
        length: EDGE_LENGTH_MAIN,
        color: { color: "#6bc1ff" }
      });
      edges.push({
        id: 1008,
        from: 4,
        to: 5,
        length: EDGE_LENGTH_MAIN,
        color: { color: "#6bc1ff" }
      });

      const vlan1 = [];
      for (let i = 6; i < 9; i++) {
        vlan1.push({
          id: i,
          label: "Computer ("+i+")",
          image: DIR + "computer.png",
          shape: "image"
        });
        edges.push({
          id: 1050 + i,
          from: 4,
          to: i,
          length: EDGE_LENGTH_MAIN,
          color: { color: "#6bc1ff" }
        });
        this.startDevices.push({
          id: i,
          label: "Computer ("+i+")",
          image: DIR + "computer.png",
          shape: "image"
        });
        this.endDevices.push({
          id: i,
          label: "Computer ("+i+")",
          image: DIR + "computer.png",
          shape: "image"
        });
      }

      const vlan2 = [
        {
          id: 9,
          label: "Mail Server (9)",
          image: DIR + "mail-server.png",
          shape: "image"
        },
        {
          id: 10,
          label: "Web Server (10)",
          image: DIR + "web-server.png",
          shape: "image"
        }
      ];
      this.endDevices.push(
        {
          id: 9,
          label: "Mail Server (9)",
          image: DIR + "mail-server.png",
          shape: "image"
        },
        {
          id: 10,
          label: "Web Server (10)",
          image: DIR + "web-server.png",
          shape: "image"
        }
      );
      edges.push({
        id: 1006,
        from: 5,
        to: 9,
        length: EDGE_LENGTH_MAIN,
        color: { color: "#6bc1ff" }
      });
      edges.push({
        id: 1005,
        from: 5,
        to: 10,
        length: EDGE_LENGTH_MAIN,
        color: { color: "#6bc1ff" }
      });

      const vlan3 = [
        {
          id: 11,
          label: "Wifi",
          image: DIR + "wifi.png",
          shape: "image"
        }
      ];
      edges.push({
        id: 1004,
        from: 4,
        to: 11,
        length: EDGE_LENGTH_MAIN,
        color: { color: "#6bc1ff" }
      });

      const smartphones = [];
      for (let i = 100; i < 103; i++) {
        smartphones.push({
          id: i,
          label: "Smartphone ("+i+")",
          image: DIR + "smartphone.png",
          shape: "image"
        });

        edges.push({
          id: 2000 + i,
          from: 11,
          to: i,
          length: EDGE_LENGTH_MAIN,
          color: { color: "#6bc1ff" }
        });
        this.endDevices.push({
          id: i,
          label: "Smartphone ("+i+")",
          image: DIR + "smartphone.png",
          shape: "image"
        });
        this.startDevices.push({
          id: i,
          label: "Smartphone ("+i+")",
          image: DIR + "smartphone.png",
          shape: "image"
        });
      }
      vlan3.push(...smartphones);

      const vlan4 = [
        {
          id: 12,
          label: "Database Server (12)",
          image: DIR + "database-server.png",
          shape: "image"
        }
      ];
      edges.push({
        id: 1002,
        from: 4,
        to: 12,
        length: EDGE_LENGTH_MAIN,
        color: { color: "#6bc1ff" }
      });
      this.endDevices.push({
        id: 12,
        label: "Database Server (12)",
        image: DIR + "database-server.png",
        shape: "image"
      });

      const vlan5 = [
        {
          id: 13,
          label: "Backup Server (13)",
          image: DIR + "database-server.png",
          shape: "image"
        }
      ];
      edges.push({
        id: 1001,
        from: 4,
        to: 13,
        length: EDGE_LENGTH_MAIN,
        color: { color: "#6bc1ff" }
      });
      this.endDevices.push({
        id: 13,
        label: "Backup Server (13)",
        image: DIR + "database-server.png",
        shape: "image"
      });

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
      this.nodesList = nodes;
      this.edgesList = edges;
      // this.edgesListA =edges;
      this.edgesList.forEach(e => {
        this.edgesActivity.push({ id: e.id, status: 0 });
      });

      this.nodes = new DataSet(nodes);
      this.edges = new DataSet(edges);
      var data = {
        nodes: this.nodes,
        edges: this.edges
      };
      var options = {
        nodes: {
          font: {
            color: "white"
          }
        }
      };
      let network = new Network(container, data, options);
    },
    recieves() {
      var devices = { start: this.startDevices, end: this.endDevices };

      this.$emit("receives", devices);
    },
    activateEdge(id) {
      this.edgesActivity.forEach(e => {
        if (e.id == id) {
          e.status += 1;
        }
      });

      var width = 1;
      var red = 30;

      setTimeout(() => {
        width += 0.5;
        red += 50;
        this.edges.update({
          id: id,
          width: width,
          color: { color: "rgb(" + red + ",50,50)" }
        });
      }, 200);
      setTimeout(() => {
        width += 0.5;
        red += 50;
        this.edges.update({
          id: id,
          width: width,
          color: { color: "rgb(" + red + ",50,50)" }
        });
      }, 400);
      setTimeout(() => {
        width += 0.5;
        red += 50;
        this.edges.update({
          id: id,
          width: width,
          color: { color: "rgb(" + red + ",50,50)" }
        });
      }, 500);
      setTimeout(() => {
        width += 0.5;
        red += 50;
        this.edges.update({
          id: id,
          width: width,
          color: { color: "rgb(" + red + ",50,50)" }
        });
      }, 600);
      setTimeout(() => {
        width += 0.5;
        red += 50;
        this.edges.update({
          id: id,
          width: width,
          color: { color: "rgb(" + red + ",50,50)" }
        });
      }, 700);
      setTimeout(() => {
        width += 0.5;
        red += 50;
        this.edges.update({
          id: id,
          width: width,
          color: { color: "rgb(" + red + ",50,50)" }
        });
      }, 800);
    },

    deactivateEdge(id) {
      // console.log("on deactivate")
      // console.log(id);

      this.edgesActivity.forEach(e => {
        // console.log(e.id,e.status)
        if (e.id == id) {
          e.status -= 1;
          if (e.status == 0) {
            this.edges.update({
              id: id,
              width: 1,
              color: { color: "#6bc1ff" }
            });
          }
        }
      });
    },
    attack(source, objective) {
      var path = [];
      path = this.explore(source, objective);

      var ids = [];
      path.forEach(element => {
        ids.push(element.id);
      });

      var i = 1;
      var intervals = 1000 / ids.length;

      for (let index = 1; index < ids.length + 1; index++) {
        setTimeout(() => {
          this.activateEdge(ids[index - 1]);
        }, index + "00");
      }
    },

    explore(src, obj) {
      console.log("exploring from " + src + " to " + obj);
      var found = false;
      var prev = 0;
      var pos = src;
      var tree = this.edgesList;
      var path = [];
      var broke = false;

      while (found == false) {
        var connections = [];
        console.log("the tree is");
        console.log(tree);

        tree.forEach(element => {
          if (element.from == pos || element.to == pos)
            connections.push(element);
        });

        connections.forEach(element => {
          if (element.from == obj || element.to == obj) {
            console.log("found the path is :");

            path.push(element);
            path.forEach(element => {
              console.log("from: " + element.from + " to : " + element.to);
            });
            found = true;
          }
        });
        if (!found) {
          if (prev != 0) {
            console.log("number of connections " + connections.length);
            connections.forEach(element => {
              console.log("from: " + element.from + " to : " + element.to);
            });

            console.log(
              "the index to be eliminated is " + connections.indexOf(prev)
            );
            connections.splice(connections.indexOf(prev), 1);
            console.log("source path eliminated");
            console.log("now its connections are :");
            connections.forEach(element => {
              console.log("from: " + element.from + " to : " + element.to);
            });
            console.log("making a total of : " + connections.length);
            console.log(connections);
          }
          if (connections.length > 0) {
            var element = connections[0];
            console.log("the new connection is ");
            console.log(element);

            if (element.from == pos) {
              pos = element.to;
            } else {
              pos = element.from;
            }
            prev = element;
            path.push(prev);
            console.log("the new prev is ");
            console.log(prev);
          } else {
            console.log("ass");
            console.log(tree.indexOf(prev));
            if (tree.indexOf(prev) == -1) {
              console.log("continue");
              broke = true;
              break;
            }
            tree.splice(tree.indexOf(prev), 1);
            pos = src;
            prev = 0;
            path = [];
          }
          // console.log("the path is: ");
          // path.forEach(element => {
          // console.log("from: " + element.from + " to : " + element.to);
          // });
        }
      }
      // tree = this.edgesList;
      if (broke) {
        console.log(src, obj);
        this.repairEdges();
        return this.explore(src, obj);
      }
      return path;
    },
    disable(source, objective) {
      var path = [];
      path = this.explore(source, objective);

      var ids = [];
      path.forEach(element => {
        ids.push(element.id);
      });
      // console.log(ids);

      ids.reverse();
      ids.forEach(element => {
        //  console.log(element);
        this.deactivateEdge(element);
      });
    },
    repairEdges(){
      var EDGE_LENGTH_MAIN = 150;
      var EDGE_LENGTH_SUB = 50;
      const DIR = "/img/network/";

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
      this.startDevices.push(internet);
      edges.push({
        id: 1000,
        from: 1,
        to: 2,
        length: EDGE_LENGTH_MAIN,
        color: { color: "#6bc1ff" }
      });

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

      edges.push({
        id: 1009,
        from: 2,
        to: 3,
        length: EDGE_LENGTH_MAIN,
        color: { color: "#6bc1ff" }
      });

      const swtch = {
        id: 4,
        label: "Switch",
        image: DIR + "switch.png",
        shape: "image"
      };

      edges.push({
        id: 1003,
        from: 3,
        to: 4,
        length: EDGE_LENGTH_MAIN,
        color: { color: "#6bc1ff" }
      });
      edges.push({
        id: 1008,
        from: 4,
        to: 5,
        length: EDGE_LENGTH_MAIN,
        color: { color: "#6bc1ff" }
      });

      const vlan1 = [];
      for (let i = 6; i < 9; i++) {
        vlan1.push({
          id: i,
          label: "Computer",
          image: DIR + "computer.png",
          shape: "image"
        });
        edges.push({
          id: 1050 + i,
          from: 4,
          to: i,
          length: EDGE_LENGTH_MAIN,
          color: { color: "#6bc1ff" }
        });
        this.startDevices.push({
          id: i,
          label: "Computer",
          image: DIR + "computer.png",
          shape: "image"
        });
        this.endDevices.push({
          id: i,
          label: "Computer",
          image: DIR + "computer.png",
          shape: "image"
        });
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
      this.endDevices.push(
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
      );
      edges.push({
        id: 1006,
        from: 5,
        to: 9,
        length: EDGE_LENGTH_MAIN,
        color: { color: "#6bc1ff" }
      });
      edges.push({
        id: 1005,
        from: 5,
        to: 10,
        length: EDGE_LENGTH_MAIN,
        color: { color: "#6bc1ff" }
      });

      const vlan3 = [
        {
          id: 11,
          label: "Wifi",
          image: DIR + "wifi.png",
          shape: "image"
        }
      ];
      edges.push({
        id: 1004,
        from: 4,
        to: 11,
        length: EDGE_LENGTH_MAIN,
        color: { color: "#6bc1ff" }
      });

      const smartphones = [];
      for (let i = 100; i < 103; i++) {
        smartphones.push({
          id: i,
          label: "Smartphone",
          image: DIR + "smartphone.png",
          shape: "image"
        });

        edges.push({
          id: 2000 + i,
          from: 11,
          to: i,
          length: EDGE_LENGTH_MAIN,
          color: { color: "#6bc1ff" }
        });
        this.endDevices.push({
          id: i,
          label: "Smartphone",
          image: DIR + "smartphone.png",
          shape: "image"
        });
        this.startDevices.push({
          id: i,
          label: "Smartphone",
          image: DIR + "smartphone.png",
          shape: "image"
        });
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
      edges.push({
        id: 1002,
        from: 4,
        to: 12,
        length: EDGE_LENGTH_MAIN,
        color: { color: "#6bc1ff" }
      });
      this.endDevices.push({
        id: 12,
        label: "Database Server",
        image: DIR + "database-server.png",
        shape: "image"
      });

      const vlan5 = [
        {
          id: 13,
          label: "Backup Server",
          image: DIR + "database-server.png",
          shape: "image"
        }
      ];
      edges.push({
        id: 1001,
        from: 4,
        to: 13,
        length: EDGE_LENGTH_MAIN,
        color: { color: "#6bc1ff" }
      });
      this.endDevices.push({
        id: 13,
        label: "Backup Server",
        image: DIR + "database-server.png",
        shape: "image"
      });

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
      // var container = document.getElementById("network");
      this.nodesList = nodes;
      this.edgesList = edges;
      
    }
  },
  mounted() {
    this.draw();
    this.recieves();
    
    // this.attack(6,7);
    // setTimeout(_ => {

    //   this.attack(1, 12);
    // }, 1000);
    // setTimeout(_ => {

    //   this.disable(1, 12);
    // }, 5000);
  }
};
</script>

<style scoped>
.Graph {
  height: 100%;
}
</style>
