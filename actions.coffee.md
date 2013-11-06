Actions
=======

    Command = require("./command")
    Palette = require("./palette")
    saveAs = require "./lib/file_saver"

    module.exports = Actions = (I={}, self=Core(I)) ->
      self.extend
        addAction: (action) ->
          self.actions.push action

        actions: Observable []

      Object.keys(Actions.defaults).forEach (hotkey) ->
        {method, icon} = Actions.defaults[hotkey]

        self.addAction
          perform: ->
            if typeof method is "function"
              method
                editor: self
            else
              self[method]()
          iconUrl: icon

        self.addHotkey hotkey, method

      return self

    state = null

    Actions.defaults =
      "ctrl+z":
        method: "undo"
        icon: "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAACRklEQVQ4T6VTXUhTYRh+p47sbhcR2zmMDGXUTVBBiyBdJUjU6EJ2K4R0ESm6CyEo6qKZWcGkoC6KFt2GxKhwi4JKbcg2khmonVW6RbmGnnI1z873c3rPjp6aQQw88PJ834H3+b73eZ7PAhv8LBvsB5PAP3pK45wDZxyYXpQZSBjHWiSUJTmlUaVQGg6feZZdO9gk6HnZqXnEw6BpAFxjWBowRGwHhSgg/5RhQc6B9FkKq0ppMOJ/FdNJTIKuFye1Q84jwLGBAzbrqOENyiQciuQX1NVYIbOQgcR0IqwUV7pfn49nTYLT0Q7NuDYDShBxTfU9rgWbCA32BrDWWZGQQ2o2Be8/Sv7RCxNDVYnovdUaJCptb9njcTILhe/yDxiPxyKxS4mjVRHos7ZeOxh0bXP1ig4RiKrCk+eRfGJgcmsFgc8HteD1nn3Y8bh/vb3Nl93BHdt39oqCAKpK4Gl0JD95/d06ggfeECV076POkV1/EzQH3EHUpL3lgMdJawgsLxVgfOxNZOrGzJ8RfPeP3XTYxC5duLmvn8pCIpkhoh1FdKKIm6zoEoqYmgJpVvJP304bIvpCx6/abY6+JrHJtFB3Y81CHQulZaiv3QzzmSwk44mwulLs/hD6Yth44k5bQLAJ5xqdjeg9GBnAouUsYJAUBRblJcjlvkF6RgqjI4Ppe/OVQWoLeoaELY4eivGdy6yOsJoDHCWPoyUZoVFKlGH95H+irP/wBPbfpYztG7sYrxDxfw+uMgdoo9u1u2+i/+2Val/pb35FXyDc5lZBAAAAAElFTkSuQmCC"
      "ctrl+y":
        method: "redo"
        icon: "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAACUUlEQVQ4T6WTUUhTURjHv+scNheypmOy7RY0H7IHC6Rgo/ItB0GXWIgSBUOJMIRFkEUlBEWBDzOsXnoQpIdqTUlrQ6k9NPBhUeCaFO0udxuB7bVc5D3n3L5zYNZSiNG9nHu+C+f/4/v+33ck+M9HqkXf9/DYRRKbHo1GgVZ0NQF6Jo9miE7SU/3xgU0Bg3Mh2TBIkBpGNyWkkxHmIIQC1Snw3WVzA8Nd/ZK/HR9KhjlkPYOzL075KDWGPVZZ2dZoB6vZCvV19UANBDAGjCEEY50SeJfLgFpQbyQvLVwRgMG5XpkZ5vH2lt2K09oKP0gZTJIZmMFQzAEUYwRwCK7FD4ugaupo6mr6ggCcjp8Iy03bI157mxCtrpVBXcnB8sqySF2UoBNwtbiBUgr5Qv5OaiQ9tF7CwLO+REfr3kCj2YIHGCSzySIejD0JPT/3Z5e6bvoyTCdvUiOvQ1UmhqZ7Sv6dBx11aIlW0iD7OTs21Z+oEnOB/9r+ywvZ9C34u40nHwdL/rYDDklCwFcNlgpLYzNn5jcANpsZ4UHvAyXRIe8JWCxbsFYs4e3LIl2jsfnzr/4JEYDjE0fCbrsn4nV5sW1oYnkVchqaWEQT0cDKHFA0VPyjke/v5YRWfJS7h2Xs9PiuHe2Ko9kJ339+gwZTg2gZbx/DORAxvnwmZqKz8PH+p98ADglEunw6YcMep0exNdlgq9UKkskEBp8FXByEEwoGgp4+moX8hFYN4JBD1/fJlBhBTLWbENZJCGlmOqvjqfP2VnaGcWGyuBFQy82snP0Ffg5KIO/aNV0AAAAASUVORK5CYII="
      "ctrl+s":
        method: "download"
        icon: "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAACs0lEQVQ4T42SS0hUURjH/2fGO2MPTC3p6VQudBEt1Bm1UWgXCEW0LNpIlk5WYoq2UYseFAmaYaRNAy2KApMkq5lRepChjJqaiUJp4cKyRTgzPsZp7jl954xEK+teDt/HPff/+57MWuwpE2DbDQx5AFLIXwuIGMbAIOgLPUa6NNARgkPnmDVp+BwKLV3rbz7QymwO7x1nVV4h6P+0rWalEVwgHKHziyvxKrMBBMTcIsdcSBcT03P6PfeEf+zrTBWzOjrH71bmprX5gqg6lCTlOH2jD9eLMxHhQKzGYNIMWCKYf0EnKzA5swAjOC64BpYkYNZZmbvucW8AFQc3qJTPNvXjyokMaEaKbjJQ6kBgUcd8iINTdq6uH8jPjENZY4+QgPDtCrvW7gugJH+9AlQ7B3GpMB2rY43QqITFMBU+r1NGEgACzCB9hxl1D96DAF7eVG5nT6mE4/sSFYA0WGM2UnSGiE7RKfWFsK7Egl6X9zt2W0xoeDQIZjvpFY2ldjzrD+Db9BQ1izpOAC2GGkewCKUcoWYsD0QFiI9PxC6LGU2twwRweEV9aQ6e9/lVrVKl5qcUAqSnyASgSy4P+QYKkrqJoeXJSBRQdyoH7gG/ov8ZPoFkw6RQzl+lT1ZIh8ApSQyujo9RwFVHFrqGAtGtoUu5Q9LqEiCjy0zI51xXO0IeLIkC991jEuARl4uy8Go4iNoj25YhK5uKllEkJwg87BwHy6Ymni+04c1IALWHk9Hw7tiK6lK7E+XNH7AlXqDt5ScClHhFTYEV3aNB1BDAN/V6RYAteS/Kbg1hc5xA+1sCUAm8usDKesYkwPJfGZy5OYCNBOjonpCb6Jk8dzRjp5zh/uzoKv/ruejyqQa/6P3yk1mL3PXU11QwsYcJJNDw1Oio3Wpsf1sZJDpWIRh4UDDjyG82p2waquUVyAAAAABJRU5ErkJggg=="
      "ctrl+b":
        method: "toDataURL"
        icon: null
      "ctrl+e":
        method: ({editor}) ->
          paletteData = Palette.export editor.palette()
          blob = new Blob [paletteData],
            type: "text/plain"
          saveAs blob, "palette.pal"
        icon: "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAAC/UlEQVQ4T4WTW0gUYRTH/98s4+66umYGdjHSXLesyMi18qEg7EHKsB6kIunBHoJuFBj4YNBDBEFBUUmUQS1FpaRlCEEldiEybdcSzLVMLc3bquvsdcaZ+Tq70eXBapgzhxm+8/v+3/+cYfjPdbhobVZ6WsqBOckJBeCaNioFnnweGrtWVe/6EC1l/6o/sWdjWVZa6nH7Ult66nwOcCu8I2H09XqGOvqGTje0N1f9DSBcPFLsXLYoaeeKhUbD7EwNhhQPdMWEqa+l0ONEfOpq1eqaWnfOCDh3cHP1Bkf+3uVzfWCyGxoXIGYbEJzWUT3AYTaYsU0vRFPri0czAmorS4JbwsnxPCCBFcrgphTIYj4GfR14au1A3DRDiboJ7n7PF+Z5uLrdaF2+jDHhlx3vngligV+EHg4hWFyArs4WyWoxipibbT78LQkWTcHtWR64vcOTrLvBEbEVtRgZIzHkU/TRcGovVq1bjwRRx4RPQpurUY8XBBafU8IqxtJgUQO4P9sN99iIn3Xfy/XZil8mKcNV0BQDIFjR3PgWSkCBqEewMicXfp8XokGASYng9aAX6fPSkJpowKN3rjvMczdPsm1/khgZcILrHLo8gfBoL4Kj41BHh//a5TYp403N444dBMhVFm9tFCddlyCP9FCrQjAYkzCv6AwNCcPAx3aYtBDMogmqNg1ZsEB6Wo6K+mB6XdOHfua5mafYd7eQYwrtppMFnJT4oUqvoEd6IctBjA/5ACrmLIRk+zF4a0thP9QW6yDzXHfIWaXP4/TQe5pUH4WfCvsIpNMdIWaYcoC+R4OUZJxHz9WtvwHdlx2RzLImo+ZvBlenaBGFSjtylYJmQAv+gKpSLFvsTvRcKfoDcMERtu17bJqefBArjEUM8hPmp/doMYFIkWXJLfTe2B+hI5hjR+g6u/rb4rKaeGWiJZG0C5xHZVNQ5ppMWSFbKEc94hqMC3bxfudRaUm5a1YM0Hky+wgThDWMs01gPPGff3d00BgUarczu7LzUHTtd5jOkNp6KQ05AAAAAElFTkSuQmCC"
      "ctrl+l":
        method: ({editor}) ->
          editor.execute Command.NewLayer(null, editor)
        icon: "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAABAAAAAQCAYAAAAf8/9hAAAB60lEQVQ4T2NkgAAZIBYHYmYoHxf1CijxCIj/wRQwQhnGP3782MvMzMzLyMjIhE33nz9/GGbNmjUpLy+vH9kQmAFmv3//Pv7z508moAFw/SBN//79Y/j16xeDsLAww+vXrxnWrl07KScnB24ISDUImwIVnwQawsDExATWBAIgA/7+/Qs2QEhICEyD5BYvXjwpMzMTZMgDFANAimEApPD///9gQ4AuYxAUFETxGQsLizlQ4DSKASANMC+A2CADQRhkCMh1IENBbDExMQasBqAHHrIhMMNAhvDz8xNnAMhAmCEgjSBDQHwuLi7iDQAZAgsPWBixsbGRZgDI1l1n1zPsv7CV4ePX9wy///xmuH39zsyjC25nogQichiANMHA1hMrGS4838NgrGXCICukyrDvygaGE1cOM9y/86aXoAEgg9In+DP4e7oBEzoTg79mHkPPnlRgmmdiWLFu0w+cBsACEEQH1JsyZEVlM3hpp8BdtenyVIbWGS3gVAhLiSegbIys4FSiyuDj6cAATNgMlW4LGNp3JjBwMLPDXQDSYAJMbQeBmYkDyMbITDO3dDLsvb2IwUrPhkFV0pDh9vPzDMcuHWF4fP8jOAxAQBaIQdkZa04EKdCJFM7m4GcNAWYVLmCS+Pbn+5+FFxa+yQIAB8Ulv4JKPAEAAAAASUVORK5CYII="
      "ctrl+r":
        method: ({editor}) ->
          if newSize = prompt("New Size (WxH)", "32x32")
            [width, height] = newSize.split("x").map (v) -> parseInt v, 10

          editor.execute Command.Resize(width, height, editor)
      "s":
        method: ({editor}) ->
          # Testing save/restore
          state = editor.saveState()
          console.log state
      "r":
        method: ({editor}) ->
          editor.restoreState state