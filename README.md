# TouchDesigner Components

Shared TouchDesigner components and project templates from HSBI Fachbereich Gestaltung.
Take what you need, add what you've built.

## What's in here

| Folder | Contains | How to use |
|---|---|---|
| `components/` | Single `.tox` components | Drag and drop into your own network |
| `templates/` | Complete projects (`.toe`) with assets and docs | Download the folder, open the `.toe` and build on it |

A component does one thing and plugs into any project. A template is a whole starting point, usually made for a specific setup such as an installation, venue or hardware.

## Using a component

You don't need Git to use anything here.

1. Open the component's folder below `components/`.
2. Click the `.tox` file and use **Download raw file** (top right).
3. Drag the `.tox` into your TouchDesigner network.

Each component has its own README with the TouchDesigner version it was built in, its inputs, outputs and parameters.

## Using a template

1. Open the template's folder below `templates/`.
2. Download the whole folder, not only the `.toe`, because templates often rely on files next to them (`assets/` and so on). Without Git, use **Code → Download ZIP** on the repository's main page.
3. Open the `.toe` in TouchDesigner and follow the template's README.

## Adding a component

1. Copy the `_template/` folder into `components/` and rename it, e.g. `components/osc-router/`.
2. Put your `.tox` in it, named like the folder (`osc-router.tox`).
3. Fill in the README in your folder. A short GIF or screenshot (`preview.gif`) helps a lot.
4. Commit and push it directly to `main`.

No Git? Open the `components/` folder on GitHub, use **Add file → Upload files** and choose **Commit directly to the `main` branch**.

Not a member of the organization yet? Your upload will automatically become a pull request, and we'll merge it. Or ask to be added.

## Adding a template

Same as a component, but in `templates/`:

1. Create a folder, e.g. `templates/medienkunstcontainer/`.
2. Put your `.toe` in it, named like the folder, together with the small files it needs.
3. Add a README that explains what the template is for, which TouchDesigner version and license it needs, and how to get started.

Remove TouchDesigner's automatic backups (`Backup/`, `*.1.toe`, …) and any helper components that only exist on your machine before uploading.

### Conventions

- Folder and file names: lowercase, words separated by hyphens (`midi-controller`, not `MidiController_final_v2`).
- One component or template per folder.
- Externalize longer Python scripts as `.py` files in a `python/` subfolder (TouchDesigner: *Sync to File*), so changes are readable.
- Don't commit large media files or rendered videos. Link to them instead.

## License

Unless a component's or template's README says otherwise, everything here is licensed under the [MIT License](LICENSE).
