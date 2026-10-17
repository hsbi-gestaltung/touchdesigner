# TouchDesigner Components

Shared TouchDesigner `.tox` components from HSBI Fachbereich Gestaltung.
Take what you need, add what you've built.

## Using a component

You don't need Git to use anything here.

1. Open the component's folder below `components/`.
2. Click the `.tox` file and use **Download raw file** (top right).
3. Drag the `.tox` into your TouchDesigner network.

Each component has its own README with the TouchDesigner version it was built in, its inputs, outputs and parameters.

## Adding a component

1. Copy the `_template/` folder into `components/` and rename it, e.g. `components/osc-router/`.
2. Put your `.tox` in it, named like the folder (`osc-router.tox`).
3. Fill in the README in your folder. A short GIF or screenshot (`preview.gif`) helps a lot.

### Conventions

- Folder and file names: lowercase, words separated by hyphens (`midi-controller`, not `MidiController_final_v2`).
- One component per folder.
- Externalize longer Python scripts as `.py` files in a `python/` subfolder (TouchDesigner: *Sync to File*), so changes are readable.
- Don't commit large media files. Link to them instead.

## License

Unless a component's README says otherwise, everything here is licensed under the [MIT License](LICENSE).
