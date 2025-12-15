<script lang="ts">
  interface Props {
    workflow: "metis" | "themis" | "tethys" | "theia";
    icon: string;
    title: string;
    description: string;
    features: string[];
    actionText: string;
    href: string;
  }

  let {
    workflow,
    icon,
    title,
    description,
    features,
    actionText,
    href,
  }: Props = $props();
</script>

<a {href} class="workflow-card {workflow}-card">
  <img src={icon} alt={title} class="card-icon" />
  <h2>{title}</h2>
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
  <div class="card-action">{actionText}</div>
</a>

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
</style>
