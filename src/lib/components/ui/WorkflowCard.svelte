<script lang="ts">
  interface Subflow {
    title: string;
    description: string;
    href: string;
  }

  interface Props {
    workflow: "metis" | "themis" | "tethys" | "theia";
    icon: string;
    title: string;
    description: string;
    features: string[];
    href?: string;
    subflows?: Subflow[];
  }

  let {
    workflow,
    icon,
    title,
    description,
    features,
    href,
    subflows = [],
  }: Props = $props();

  let expanded = $state(false);

  // Build subflows array - if href provided with no subflows, create single subflow
  const effectiveSubflows =
    subflows.length > 0
      ? subflows
      : href
        ? [{ title: title, description: description, href }]
        : [];

  function toggleExpanded(e: MouseEvent) {
    e.preventDefault();
    expanded = !expanded;
  }
</script>

<div
  class="workflow-card {workflow}-card {expanded ? 'expanded' : ''}"
  role="button"
  tabindex="0"
  onclick={toggleExpanded}
  onkeydown={(e) => {
    if (e.key === 'Enter' || e.key === ' ') {
      e.preventDefault();
      toggleExpanded(e);
    }
  }}
>
  <!-- Always visible -->
  <img src={icon} alt={title} class="card-icon" />
  <h2>{title}</h2>

  {#if !expanded}
    <!-- Visible when inactive -->
    <p>{description}</p>
    <div class="card-features">
      {#if features.length > 0 && features[0] === "Coming Soon"}
        <pre>Coming Soon</pre>
      {:else}
        {#each features as feature}
          <span>{feature}</span>
        {/each}
      {/if}
    </div>
  {:else}
    <!-- Visible when active -->
    <div class="subflows-container">
      {#each effectiveSubflows as subflow}
        <a
          href={subflow.href}
          class="subflow-button"
          onclick={(e) => e.stopPropagation()}
        >
          <strong>{subflow.title}</strong>
          <span>{subflow.description}</span>
        </a>
      {/each}
    </div>
    <div class="card-action">Collapse ↑</div>
  {/if}
</div>

<style>
  .workflow-card {
    border-radius: 12px;
    padding: 2.5rem;
    box-shadow: 0 4px 16px rgba(0, 0, 0, 0.1);
    text-decoration: none;
    color: inherit;
    display: flex;
    flex-direction: column;
    transition: all 0.3s ease;
    border: 2px solid transparent;
    cursor: pointer;
  }

  /* Remove cursor pointer for link cards */
  a.workflow-card {
    cursor: pointer;
  }

  div.workflow-card {
    user-select: none;
  }

  div.workflow-card:focus {
    outline: 2px solid var(--palette-foreground);
    outline-offset: 2px;
  }

  /* Each card uses its own workflow palette background */
  .metis-card {
    background: var(--metis-bg-subtle);
  }

  .themis-card {
    background: var(--themis-bg-subtle);
  }

  .tethys-card {
    background: var(--tethys-bg-subtle);
  }

  .theia-card {
    background: var(--theia-bg-subtle);
  }

  .workflow-card:hover {
    transform: translateY(-4px);
    box-shadow: 0 8px 24px rgba(0, 0, 0, 0.15);
  }

  .metis-card:hover {
    border-color: var(--metis-fg-alt-dark);
  }

  .tethys-card:hover {
    border-color: var(--tethys-fg-alt-dark);
  }

  .themis-card:hover {
    border-color: var(--themis-fg-alt-dark);
  }

  .theia-card:hover {
    border-color: var(--theia-fg-alt-dark);
  }

  .card-icon {
    width: 80px;
    height: 80px;
    object-fit: contain;
    margin-bottom: 1rem;
  }

  .workflow-card h2 {
    font-size: 1.75rem;
    color: var(--palette-foreground);
    margin: 0 0 1rem 0;
  }

  .workflow-card p {
    color: var(--palette-foreground);
    line-height: 1.6;
    margin-bottom: 1.5rem;
    flex-grow: 1;
  }

  .card-features {
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
    margin-bottom: 1.5rem;
    padding: 1rem;
    background: var(--palette-bg-nav);
    border-radius: 8px;
  }

  .card-features span {
    color: var(--palette-foreground);
    font-size: 0.9rem;
    opacity: 0.9;
  }

  .card-action {
    color: var(--metis-fg-alt-dark);
    font-weight: 600;
    font-size: 1.1rem;
    text-align: center;
    padding: 1rem;
    background: color-mix(in srgb, var(--metis-fg-alt-dark) 10%, transparent);
    border-radius: 8px;
    transition: all 0.3s;
  }

  .themis-card .card-action {
    color: var(--themis-fg-alt-dark);
    background: color-mix(in srgb, var(--themis-fg-alt-dark) 10%, transparent);
  }

  .tethys-card .card-action {
    color: var(--tethys-fg-alt-dark);
    background: color-mix(in srgb, var(--tethys-fg-alt-dark) 10%, transparent);
  }

  .theia-card .card-action {
    color: var(--theia-fg-alt-dark);
    background: color-mix(in srgb, var(--theia-fg-alt-dark) 10%, transparent);
  }

  .themis-card:hover .card-action {
    background: var(--themis-fg-alt-dark);
    color: white;
  }

  .tethys-card:hover .card-action {
    background: var(--tethys-fg-alt-dark);
    color: white;
  }

  .metis-card:hover .card-action {
    background: var(--metis-fg-alt-dark);
    color: white;
  }

  .theia-card:hover .card-action {
    background: var(--theia-fg-alt-dark);
    color: white;
  }

  /* Subflows container */
  .subflows-container {
    display: flex;
    flex-direction: column;
    gap: 0.75rem;
    margin-bottom: 1.5rem;
  }

  .subflow-button {
    display: flex;
    flex-direction: column;
    gap: 0.25rem;
    padding: 1rem;
    background: var(--palette-bg-nav);
    border: 2px solid transparent;
    border-radius: 8px;
    text-decoration: none;
    color: inherit;
    transition: all 0.2s ease;
  }

  .subflow-button:hover {
    border-color: var(--palette-foreground);
    transform: translateX(4px);
  }

  .subflow-button strong {
    color: var(--palette-foreground);
    font-size: 1.1rem;
  }

  .subflow-button span {
    color: var(--palette-foreground);
    opacity: 0.8;
    font-size: 0.9rem;
  }

  /* Workflow-specific subflow button hover states */
  .metis-card .subflow-button:hover {
    border-color: var(--metis-fg-alt-dark);
    background: color-mix(in srgb, var(--metis-fg-alt-dark) 5%, var(--palette-bg-nav));
  }

  .themis-card .subflow-button:hover {
    border-color: var(--themis-fg-alt-dark);
    background: color-mix(in srgb, var(--themis-fg-alt-dark) 5%, var(--palette-bg-nav));
  }

  .tethys-card .subflow-button:hover {
    border-color: var(--tethys-fg-alt-dark);
    background: color-mix(in srgb, var(--tethys-fg-alt-dark) 5%, var(--palette-bg-nav));
  }

  .theia-card .subflow-button:hover {
    border-color: var(--theia-fg-alt-dark);
    background: color-mix(in srgb, var(--theia-fg-alt-dark) 5%, var(--palette-bg-nav));
  }
</style>
