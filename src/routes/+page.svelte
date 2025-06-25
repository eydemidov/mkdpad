<script lang="ts">
  import { onMount } from "svelte";
  import { EditorView } from "@codemirror/view";
  import { EditorState } from "@codemirror/state";
  import { markdown } from "@codemirror/lang-markdown";
  import { keymap } from "@codemirror/view";
  import { defaultKeymap } from "@codemirror/commands";
  import { lineNumbers } from "@codemirror/view";
  import { highlightActiveLineGutter } from "@codemirror/view";
  import { highlightSpecialChars } from "@codemirror/view";
  import { drawSelection } from "@codemirror/view";
  import { dropCursor } from "@codemirror/view";
  import { rectangularSelection } from "@codemirror/view";
  import { crosshairCursor } from "@codemirror/view";
  import { highlightActiveLine } from "@codemirror/view";
  import { bracketMatching } from "@codemirror/language";
  import { closeBrackets } from "@codemirror/autocomplete";
  import { search } from "@codemirror/search";
  import { autocompletion } from "@codemirror/autocomplete";
  import { searchKeymap } from "@codemirror/search";
  import { history } from "@codemirror/commands";
  import { vim } from "@replit/codemirror-vim";
  import AISidebar from "../lib/components/AISidebar.svelte";

  let editorElement: HTMLElement;
  let editorView: EditorView;

  function getEditorContent(): string {
    if (editorView) {
      return editorView.state.doc.toString();
    }
    return "";
  }

  function getSelectedText(): string {
    if (editorView) {
      const selection = editorView.state.selection;

      if (selection.ranges.length > 0) {
        const range = selection.ranges[0];
        if (range.from !== range.to) {
          const selectedText = editorView.state.doc.sliceString(
            range.from,
            range.to,
          );
          return selectedText;
        } else {
        }
      } else {
      }
    } else {
    }
    return "";
  }

  onMount(() => {
    const state = EditorState.create({
      doc: "# Welcome to MkdPad\n\nStart writing your markdown here...",
      extensions: [
        vim(),
        history(),
        lineNumbers(),
        highlightActiveLineGutter(),
        highlightSpecialChars(),
        drawSelection(),
        dropCursor(),
        EditorState.allowMultipleSelections.of(true),
        rectangularSelection(),
        crosshairCursor(),
        highlightActiveLine(),
        bracketMatching(),
        closeBrackets(),
        autocompletion(),
        search(),
        markdown(),
        keymap.of([...defaultKeymap, ...searchKeymap]),
        EditorView.theme({
          "&": {
            fontSize: "14px",
            fontFamily:
              'ui-monospace, SFMono-Regular, "SF Mono", Consolas, "Liberation Mono", Menlo, monospace',
            height: "100%",
            display: "flex",
            flexDirection: "column",
          },
          ".cm-editor": {
            height: "100%",
            outline: "none",
            backgroundColor: "#1a1a1a",
            display: "flex",
            flexDirection: "column",
          },
          ".cm-scroller": {
            height: "100%",
            overflow: "auto",
          },
          ".cm-content": {
            padding: "20px",
            caretColor: "#e5e5e5",
            color: "#e5e5e5",
            height: "100%",
          },
          ".cm-line": {
            lineHeight: "1.6",
            padding: "0 4px",
          },
          ".cm-gutters": {
            backgroundColor: "#0f0f0f",
            color: "#6b7280",
            border: "none",
            paddingRight: "8px",
          },
          ".cm-activeLineGutter": {
            backgroundColor: "#2d2d2d",
            color: "#d1d5db",
          },
          ".cm-activeLine": {
            backgroundColor: "#2d2d2d",
          },
          ".cm-selectionBackground": {
            backgroundColor: "#404040",
          },
          ".cm-cursor": {
            borderLeftColor: "#e5e5e5",
            borderLeftWidth: "2px",
          },
          ".cm-tooltip": {
            backgroundColor: "#262626",
            color: "#e5e5e5",
            border: "1px solid #404040",
            borderRadius: "4px",
            boxShadow: "0 4px 12px rgba(0, 0, 0, 0.4)",
          },
          ".cm-tooltip.cm-tooltip-autocomplete": {
            backgroundColor: "#262626",
            border: "1px solid #404040",
            borderRadius: "4px",
            boxShadow: "0 4px 12px rgba(0, 0, 0, 0.4)",
          },
          ".cm-tooltip.cm-tooltip-autocomplete > ul": {
            backgroundColor: "#262626",
            maxHeight: "200px",
            overflow: "auto",
          },
          ".cm-tooltip.cm-tooltip-autocomplete > ul > li": {
            color: "#e5e5e5",
            padding: "4px 8px",
            borderBottom: "1px solid #404040",
          },
          ".cm-tooltip.cm-tooltip-autocomplete > ul > li[aria-selected]": {
            backgroundColor: "#404040",
            color: "#ffffff",
          },
          ".cm-tooltip.cm-tooltip-autocomplete > ul > li:hover": {
            backgroundColor: "#374151",
          },
          ".cm-searchMatch": {
            backgroundColor: "#525252",
          },
          ".cm-searchMatch.cm-searchMatch-selected": {
            backgroundColor: "#6b7280",
          },
          ".cm-panel": {
            backgroundColor: "#262626",
            color: "#e5e5e5",
            border: "1px solid #404040",
          },
          ".cm-panel.cm-panel-top": {
            borderBottom: "1px solid #404040",
          },
          ".cm-panel.cm-panel-bottom": {
            borderTop: "1px solid #404040",
          },
        }),
      ],
    });

    editorView = new EditorView({
      state,
      parent: editorElement,
    });

    return () => {
      editorView?.destroy();
    };
  });
</script>

<div class="h-screen flex bg-neutral-900 min-h-screen">
  <div class="flex-1 flex flex-col">
    <div
      class="h-12 border-b border-neutral-700 flex items-center px-4 bg-neutral-800"
    >
      <h1 class="text-sm font-medium text-neutral-300">MkdPad</h1>
    </div>

    <div class="flex-1 relative overflow-hidden">
      <div
        bind:this={editorElement}
        class="h-full w-full absolute inset-0"
      ></div>
    </div>
  </div>

  <div class="w-80 border-l border-neutral-700 bg-neutral-800">
    <AISidebar {getEditorContent} {getSelectedText} />
  </div>
</div>
