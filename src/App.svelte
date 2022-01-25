<script lang="ts">
  import { onMount } from "svelte";
  import * as d3 from "d3";
  import { escape } from "svelte/internal";

  let _div: d3.Selection<d3.BaseType, unknown, HTMLElement, any>;
  let div = _div as unknown | SVGElement;
  let output: d3.Selection<HTMLOutputElement, unknown, HTMLElement, any>;

  onMount(() => {
    _div = d3.select("div");

    let p = _div.append("p");
    p.text("画像を含むディレクトリで試して下さい。");

    let input = _div.append("input");
    input
      .attr("type", "file")
      .attr("id", "files3")
      .attr("name", "files3[]")
      .attr("multiple", true)
      .on("change", function (event) {
        handleFileSelect(event);
      });

    _div.append("br");

    output = _div.append("output");
    output.attr("id", "thumbnails");
  });

  function handleFileSelect(event: Event) {
    Array.from((event.target as HTMLInputElement).files).forEach((f) => {
      if (f.type.match("image.*")) {
        let reader = new FileReader();
        reader.readAsDataURL(f);

        //This function is under slow thread.
        reader.onload = (e) => {
          let span = output.append("span");
          let img = span.append("img");
          img
            .attr("class", "thumb")
            .attr("title", escape(f.name))
            .attr("src", String(e.target.result));
        };
      }
    });
  }
</script>

<div bind:this={div} class="example" />

<style lang="scss">
  .example {
    padding: 10px;
    border: 1px solid #ccc;
    :global(#thumbnails) {
      :global(span) {
        :global(.thumb) {
          height: 75px;
          border: 1px solid #000;
          margin: 10px 5px 0 0;
        }
      }
    }
  }
</style>
