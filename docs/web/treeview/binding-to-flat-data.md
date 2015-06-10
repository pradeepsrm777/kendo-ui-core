
    <div id="tree"></div>
    <script>
    var flatData = [
      { id: 1, parent: 0, text: "Item 1" },
      { id: 2, parent: 0, text: "Item 2" },
      { id: 3, parent: 0, text: "Item 3" },
      { id: 4, parent: 0, text: "Item 4" },
      { id: 5, parent: 1, text: "Item 1.1" },
      { id: 6, parent: 1, text: "Item 1.2" },
      { id: 7, parent: 1, text: "Item 1.3" },
      { id: 8, parent: 3, text: "Item 3.1" },
      { id: 9, parent: 3, text: "Item 3.2" },
      { id: 10, parent: 5, text: "Item 1.1.1" },
      { id: 11, parent: 5, text: "Item 1.1.2" },
      { id: 12, parent: 5, text: "Item 1.1.3" }
    ];

    function processTable(data, idField, foreignKey, rootLevel) {
      var hash = {};

      for (var i = 0; i < data.length; i++) {
        var item = data[i];
        var id = item[idField];
        var parentId = item[foreignKey];

        hash[id] = hash[id] || [];
        hash[parentId] = hash[parentId] || [];

        item.items = hash[id];
        hash[parentId].push(item);
      }

      return hash[rootLevel];
    }

    // tree for visualizing data
   $("#tree").kendoTreeView({
                    dataSource: processTable(
                        flatData = new kendo.data.DataSource({
                        type: "json",
                        transport: {
                            read: {
                                url: "@Url.Action("ActionName","Controllername")"
                        }
                    },
                        schema: {
                    data: "Data",
                    }
                }), "id", "parent", 0),
                    loadOnDemand: false
                });
    </script>

## Method 2: Incremental data filtering

The second method allows nodes to be loaded on demand, but is more computationally intensive, as it requires the collection to be filtered each time a node gets expanded.

    <div id="tree"></div>
    <script>
   // var flatData = [
     // { id: 1, parent: null, text: "Item 1" },
     // { id: 2, parent: null, text: "Item 2" },
      //{ id: 3, parent: null, text: "Item 3" },
      //{ id: 4, parent: null, text: "Item 4" },
      //{ id: 5, parent: 1, text: "Item 1.1" },
      //{ id: 6, parent: 1, text: "Item 1.2" },
      //{ id: 7, parent: 1, text: "Item 1.3" },
      //{ id: 8, parent: 3, text: "Item 3.1" },
      //{ id: 9, parent: 3, text: "Item 3.2" },
      //{ id: 10, parent: 5, text: "Item 1.1.1" },
      //{ id: 11, parent: 5, text: "Item 1.1.2" },
      //{ id: 12, parent: 5, text: "Item 1.1.3" }
    //];

    // tree for visualizing data
    $("#tree").kendoTreeView({
      dataSource: {
        transport: {
          read: function(options) {
            var id = options.data.id || null;

            options.success($.grep(flatData, function(x) {
              return x.parent == id;
            }));
          }
        },
        schema: {
          model: {
            id: "id",
            hasChildren: function(x) {
              var id = x.id;

              for (var i = 0; i < flatData.length; i++) {
                if (flatData[i].parent == id) {
                  return true;
                }
              }
              return false;
            }
          }
        }
      }
    })
    </script>
