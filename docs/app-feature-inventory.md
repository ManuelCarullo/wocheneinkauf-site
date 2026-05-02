# Wocheneinkauf App Feature Inventory

Source document for future website, App Store, support, and general user documentation copy.
Collected from the iOS app codebase on 2026-05-01.
Last updated: 2026-05-01.

## Product Summary

Wocheneinkauf is an iOS grocery planning app for households. It keeps grocery libraries, catalog items, recipes, shopping plans, active shopping runs, and shopping history together in one app. A household can plan from saved recipes and recurring basics, customize the resulting list, share the library through iCloud, and check items off while shopping.

The app is built around user-managed libraries. Each library contains its own catalog, recipes, selections, category order, active run, and history. Libraries can be private, shared with other iCloud users, or accepted from someone else.

## Core User Promise

- Turn recipes and household basics into a practical grocery plan.
- Keep reusable grocery catalog items organized by category.
- Reuse recipes, amounts, and default item quantities instead of rebuilding the same list every week.
- Share a grocery library with household members through iCloud.
- Run the shop inside the app with checkoff, remove, finish, and discard actions.
- Restore previous shopping plans from history.

## Audience

- Households that shop from recurring recipes and staple items.
- Couples, families, roommates, or shared households that need one grocery source of truth.
- Users who want a lightweight app-centered shopping workflow rather than a generic notes list.

## App Structure

The current app has five main tabs:

- `Recipes`: manage reusable recipes and ingredients.
- `Catalog`: manage reusable grocery items.
- `Plan`: build the next shopping plan from recipes and basics.
- `Shop`: work through the active in-app shopping run.
- `Settings`: manage libraries, sharing, profile name, and appearance.

Sources: `GroceryListBuilder/GroceryListBuilder/App/ContentView.swift`, `GroceryListBuilder/README.md`.

## Feature Inventory

### Profile Onboarding

- First launch is gated by a profile onboarding screen.
- The user chooses a display name before using the main app.
- The app can seed the display name from the device name.
- The display name is used in shared planning metadata so household members can see who changed a selection or amount.
- Onboarding includes a preview of how the name appears in a shopping context.

Sources: `App/ContentView.swift`, `App/ProfileOnboardingView.swift`, `App/ProfileIdentitySettings.swift`, `App/ActorDisplayNameDefaults.swift`.

### Libraries

- Users start by creating a library or accepting a shared library.
- A valid empty-library state exists until a library is created or accepted.
- Users can create multiple libraries.
- Users can switch the active library in Settings.
- Libraries are grouped as owned and shared in Settings.
- Owned libraries can be renamed.
- Owned libraries can be deleted only when at least one other library remains.
- Shared libraries show owner and permission context where available.

Sources: `App/ContentView.swift`, `Features/Settings/Views/SQLiteSettingsView.swift`, `Features/Settings/Views/SQLiteSettingsLibraryPresentation.swift`, `App/UI/Components/SQLiteLibraryEditorSheet.swift`, `Domain/SQLite/SQLiteLibrarySelectionState.swift`.

### iCloud Sync And Sharing

- The app uses SQLiteData with CloudKit sync.
- App data syncs through the CloudKit container `iCloud.dev.carullo.grocerysqlitedata`.
- Synced data includes libraries, catalog items, item aliases, recipes, recipe ingredients, shopping selections, category order, active runs, run entries, and history.
- Owned libraries can be shared through Apple's CloudKit sharing UI.
- Sharing uses private/read-write permissions.
- Incoming shared libraries are accepted through the app and scene delegate paths.
- The app checks iCloud libraries during startup before continuing.
- Sync and sharing errors are surfaced as user-facing alerts.
- Runtime maintenance runs at launch and foreground activation to repair sync-induced duplicate or orphaned data.

Sources: `App/GroceryListBuilderApp.swift`, `App/CloudKitSharingLifecycle.swift`, `Features/Settings/Views/SQLiteSettingsView.swift`, `Domain/SQLite/SQLitePersistence.swift`, `docs/SQLiteData-Dedupe-and-Maintenance.md`.

### Catalog

- Catalog items are scoped to the active library.
- Items are listed alphabetically inside localized categories.
- Categories show item counts and category icons.
- Users can add new items.
- Users can open item details from the catalog.
- Users can delete items from the catalog.
- Item deletion is blocked when recipes still reference the item.
- Item names are normalized for duplicate detection, including diacritic folding and lowercase keys; new items and renamed items are rejected when that normalized name already exists in the library.

Sources: `Features/Items/Views/SQLiteItemsView.swift`, `Features/Items/Views/SQLiteItemDetailView.swift`, `Domain/DataAccess/ItemCatalog.swift`, `Domain/SQLite/SQLiteFeatureRepositories.swift`.

### Item Details

- Items have a name.
- Items have a grocery category.
- Items can store a default amount.
- Default amount units include grams, kilograms, milliliters, and pieces.
- Amount entry supports parsing typed values and optional units.
- Items have an availability flag for recipe use.
- Existing item detail screens show which recipes use the item.
- Existing item detail screens show shopping run usage metrics.
- Items that are in use show delete guidance and cannot be deleted from the detail screen.

Sources: `Features/Items/Views/SQLiteItemDetailView.swift`, `Features/Items/Views/SQLiteItemDetailSections.swift`, `Domain/ValueTypes/ItemAmount.swift`, `Domain/Logic/ItemAmountFormatting.swift`.

### Item Categories

Current item categories:

- Produce
- Fruit
- Baked Goods
- Baking Supplies
- Deli/Prepared Foods
- Dairy
- Protein
- Canned & Jarred
- Pantry
- Spices
- Sauces
- Oils & Vinegars
- Snacks
- Frozen
- Beverages
- Alcohol
- Household
- Toiletries
- Other

Categories have localized titles and system symbols for UI grouping.

Source: `Domain/ValueTypes/ItemCategory.swift`.

### Recipes

- Recipes are scoped to the active library.
- Recipes are listed alphabetically.
- Users can create, edit, and delete recipes.
- Recipe rows show ingredient count.
- Recipe rows show shopping run metrics, including number of shopping runs and last run date.
- Recipe detail shows the recipe name, ingredient count, shopping run metrics, and a read-only ingredient list.
- Recipe detail supports edit and delete actions.
- Deleting a recipe asks for confirmation.

Sources: `Features/Recipes/Views/SQLiteRecipesView.swift`, `Features/Recipes/Views/SQLiteRecipeDetailView.swift`, `Domain/ValueTypes/ExportMetricsSummary.swift`, `Domain/ValueTypes/ExportMetricsFormatting.swift`.

### Recipe Editor And Ingredient Picker

- Recipe editor supports new and existing recipes.
- A recipe requires a non-empty name.
- Ingredients are chosen from catalog items.
- The ingredient picker supports search.
- The picker suggests frequently used items.
- The picker lets users create a missing catalog item from the search text.
- Items already in the recipe are marked and disabled.
- New ingredients inherit the catalog item's default amount when present.
- Ingredients can be assigned amounts and units.
- Ingredients can be reordered.
- Ingredients can be removed.
- Duplicate ingredients are prevented.

Sources: `Features/Recipes/Views/SQLiteRecipeEditorView.swift`, `Features/Recipes/Views/Editor/SQLiteRecipeEditorIngredientsSection.swift`, `Features/Recipes/Views/Editor/RecipeEditorIngredientRow.swift`, `Features/Items/Views/SQLiteIngredientPickerView.swift`, `Features/Items/Views/SQLiteItemPickerPresentation.swift`.

### Shopping Plan

- The Plan tab builds the next shopping plan.
- The plan screen shows a draft summary.
- Users can select recipes.
- Users can expand selected recipes to review ingredients.
- Users can skip individual recipe ingredients.
- Users can override recipe ingredient amounts and units for the current plan.
- Empty recipes are disabled for shopping selection.
- Users can select basics from catalog items.
- Basics are grouped by category.
- Users can edit basic amounts and units for the current plan.
- Basics contributed by selected recipes are labeled so users know they are already included.
- Users can clear the current draft.
- Users can open shopping history from the plan screen.
- Users can edit category order for the plan.
- The Start Shopping action starts a new run when no active run exists.
- When a run is already active, the Plan action changes to Add to Shopping and appends only selections not already represented by open Shop rows.
- After starting or appending to a run, the current Plan selections are cleared.
- Shared-change metadata can show who last changed a selection or amount.

Sources: `Features/ShoppingList/Views/SQLiteShoppingListView.swift`, `Features/ShoppingList/Views/SQLiteShoppingListDraftStore.swift`, `Features/ShoppingList/Views/Sections/SQLiteShoppingListRecipesSection.swift`, `Features/ShoppingList/Views/Sections/SQLiteShoppingListBasicsSection.swift`.

### Shopping List Generation

- The generated list combines selected recipe ingredients and selected basics.
- Skipped recipe ingredients are omitted.
- Items are merged by category, normalized item name, and compatible base unit.
- Compatible amounts are combined.
- Kilograms and grams normalize to the same base unit for combining.
- Milliliters and pieces remain distinct base units.
- Results are sorted by category order and then item name.

Source: `Domain/Logic/ShoppingListBuilder.swift`.

### Active Shopping Runs

- The Shop tab shows the active in-app shopping run for the current library.
- Each library has at most one active run.
- Starting a run creates a dated Shopping Run.
- Runs store recipe count, basic count, selected snapshot data, contributing recipe IDs, and contributing item IDs.
- Run entries include category, title, optional amount description, and sort order.
- Run entries store normalized item identity and amount base data so appended plan items can merge predictably with open rows.
- Users can check off items.
- Users can uncheck completed items.
- Users can remove lines from the active run.
- Completed items are grouped in a disclosure section.
- Remaining items are grouped by category.
- Users can edit category order for the active run.
- Users can finish a run.
- Users can discard a run.
- Finish and discard actions update history and remove the active run.

Sources: `Features/ShoppingList/Views/SQLiteShoppingRunView.swift`, `Features/ShoppingList/Services/SQLiteShoppingRunService.swift`, `Domain/SQLite/SQLiteTables.swift`.

### Shopping History

- History is scoped to each library.
- History stores snapshots of shopping plans/runs.
- History entries track status: active, finished, or discarded.
- History rows show date, title, status, recipe/basic summary counts, and contributor display name when available.
- Users can restore a history entry back into the current shopping draft.
- Restoring a history entry asks before replacing an existing draft selection.
- Restores can partially succeed when old recipe/item references no longer exist.
- Users can delete non-active history entries.
- History contributes usage metrics for recipes and items.

Sources: `Features/ShoppingList/Views/SQLiteShoppingListHistoryView.swift`, `Features/ShoppingList/Services/SQLiteShoppingListHistoryService.swift`, `Features/ShoppingList/Model/ShoppingListHistorySnapshot.swift`, `Domain/ValueTypes/ExportMetricsSummary.swift`.

### Settings

- Settings shows library selection and library management.
- Users can create a library.
- Users can rename owned libraries.
- Users can manage sharing for owned libraries.
- Shared-with-me libraries show an informational sharing row instead of owner sharing controls.
- Users can delete owned libraries when deletion is allowed.
- Users can edit their profile display name.
- Users can choose appearance mode with a segmented picker.
- Debug builds include a diagnostics entry.

Sources: `Features/Settings/Views/SQLiteSettingsView.swift`, `Features/Settings/Views/SQLiteSettingsRows.swift`, `Features/Settings/Views/SQLiteSettingsLibraryPresentation.swift`, `Features/Settings/Views/SQLiteDiagnosticsView.swift`.

### Appearance

- The app supports system, light, and dark appearance modes.
- The selected appearance mode is stored in app storage.
- The setting uses a segmented picker in Settings.

Sources: `Features/Settings/Views/SQLiteSettingsView.swift`, `App/GroceryListBuilderApp.swift`.

### Localization

- The string catalog source language is English.
- English and German localizations are present.
- German copy uses informal "Du" per app instructions.
- User-facing strings are expected to live in `Localizable.xcstrings`.

Sources: `GroceryListBuilder/Localizable.xcstrings`, `GroceryListBuilder/AGENTS.md`.

### Accessibility And Native iOS Behavior

- The app uses native SwiftUI controls such as tabs, navigation stacks, lists, forms, toggles, menus, disclosure groups, confirmation dialogs, and sheets.
- Several shopping and category reorder controls include explicit accessibility labels, values, hints, and accessibility move actions.
- Toasts announce messages through UIAccessibility.
- The app uses haptic success feedback after saves and important shopping/history actions.
- The profile onboarding layout adapts for Dynamic Type and hides its preview at large accessibility sizes or constrained heights.

Sources: `App/UI/Toast.swift`, `App/ProfileOnboardingView.swift`, `Features/ShoppingList/Views/Sections/SQLiteShoppingListBasicsSection.swift`, `Features/ShoppingList/Views/Sections/SQLiteShoppingListRecipesSection.swift`, `Features/ShoppingList/Views/SQLiteShoppingRunView.swift`.

## Privacy-Relevant Facts For Website Copy

- The app stores grocery libraries, catalog items, recipes, shopping selections, active shopping runs, history entries, profile display name, and local settings.
- App data is stored through the app's SQLiteData runtime.
- If iCloud is enabled, app data can sync through Apple CloudKit.
- Shared libraries are only shared through Apple iCloud sharing.
- The profile display name can be visible to people sharing a library because it is used for change attribution and history attribution.
- The codebase search found no analytics, ads, or third-party tracking SDK references in the app source.
- Privacy-sensitive data flows found in the app are local app storage, iCloud/CloudKit sync and sharing, the display-name default from device name, and shared shopping audit metadata.

Sources: `Domain/SQLite/SQLiteTables.swift`, `App/GroceryListBuilderApp.swift`, `App/ProfileIdentitySettings.swift`, `App/ActorDisplayNameDefaults.swift`, `Domain/ValueTypes/ShoppingSelectionMetadataFormatter.swift`, `wocheneinkauf-site/privacy/index.html`, `wocheneinkauf-site/datenschutz/index.html`.

## Suggested Marketing Angles

### Primary Positioning

Wocheneinkauf is a shared household grocery planner that turns recipes and everyday basics into an in-app shopping run.

### Short Value Propositions

- Plan from recipes and staples in one place.
- Share one grocery library with your household.
- Customize each shop without editing the original recipe.
- Combine duplicate ingredients automatically.
- Check items off in a focused shopping run.
- Restore past shopping plans when a week repeats.

### Feature Callouts

- Shared household libraries with iCloud.
- Recipe-based grocery planning.
- Basics for recurring staples.
- Smart merging of duplicate ingredients and compatible amounts.
- Category-based shopping organization.
- Editable category order for planning and shopping.
- Shopping history with restore.
- English and German localization.
- No analytics or ads found in app code.

### Example Website Sections

- "From recipes to a real shopping run": recipe selection, skipped ingredients, amount overrides, start shopping.
- "One library for the household": iCloud sharing, owned/shared libraries, display names for changes.
- "Catalog once, reuse every week": item defaults, categories, recipe usage, frequent ingredient suggestions.
- "Shop without rebuilding the list": checkoff, completed section, remove items, finish/discard.
- "Repeat what worked": history restore and recipe/item usage metrics.

### Possible App Store Keywords

grocery list, shopping list, meal planning, recipes, household, shared list, iCloud, pantry, weekly shopping, groceries, family, ingredients, staples

## User Documentation Topics To Write Later

- Getting started: choose a display name, create the first library.
- Creating and switching libraries.
- Sharing a library with another iCloud user.
- Accepting a shared library.
- Adding catalog items and default amounts.
- Creating a recipe.
- Adding ingredients from the catalog.
- Creating a new ingredient while editing a recipe.
- Planning a shop from recipes.
- Adding basics to a plan.
- Skipping ingredients for one shop.
- Editing amounts for one shop.
- Starting and finishing a shopping run.
- Restoring a previous plan from history.
- Understanding shared change labels.
- Troubleshooting iCloud sync and read-only shared libraries.

## Documentation Notes

- `GroceryListBuilder/README.md` has been updated to describe the current five-tab app structure: Recipes, Catalog, Plan, Shop, Settings.
- `GroceryListBuilder/README.md` no longer references the removed history-retention setting or missing CloudKit rollout document.
- Reminders/export wording remains in some localization and table fields, but no EventKit/Reminders integration code or `EventKit` imports were found in app source.
- Website copy should avoid promising Reminders export unless the feature is reintroduced or confirmed elsewhere.

## Source Map

- App entry and tabs: `GroceryListBuilder/GroceryListBuilder/App/ContentView.swift`
- App bootstrap and CloudKit: `GroceryListBuilder/GroceryListBuilder/App/GroceryListBuilderApp.swift`
- Share acceptance: `GroceryListBuilder/GroceryListBuilder/App/CloudKitSharingLifecycle.swift`
- Profile onboarding: `GroceryListBuilder/GroceryListBuilder/App/ProfileOnboardingView.swift`
- SQLite tables: `GroceryListBuilder/GroceryListBuilder/Domain/SQLite/SQLiteTables.swift`
- Persistence and sync setup: `GroceryListBuilder/GroceryListBuilder/Domain/SQLite/SQLitePersistence.swift`
- Shopping list builder: `GroceryListBuilder/GroceryListBuilder/Domain/Logic/ShoppingListBuilder.swift`
- Categories and units: `GroceryListBuilder/GroceryListBuilder/Domain/ValueTypes/ItemCategory.swift`, `GroceryListBuilder/GroceryListBuilder/Domain/ValueTypes/ItemAmount.swift`
- Recipes: `GroceryListBuilder/GroceryListBuilder/Features/Recipes/`
- Catalog/items: `GroceryListBuilder/GroceryListBuilder/Features/Items/`
- Shopping plan/run/history: `GroceryListBuilder/GroceryListBuilder/Features/ShoppingList/`
- Settings: `GroceryListBuilder/GroceryListBuilder/Features/Settings/`
- Localizations: `GroceryListBuilder/GroceryListBuilder/Localizable.xcstrings`
