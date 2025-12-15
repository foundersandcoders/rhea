<script lang="ts">
  import { onMount } from "svelte";
  import type { Arc } from "$lib/types/themis";
  import { savedCourses } from "$lib/stores/themisStores";
  import ThemeSelector from "$lib/components/ui/ThemeSelector.svelte";
  import WorkflowCard from "$lib/components/ui/WorkflowCard.svelte";

  function countModules(arcs: Arc[]): number {
    let total = 0;
    arcs.forEach((arc) => {
      total += arc.modules.length;
    });
    return total;
  }

  onMount(() => {
    console.log("Welcome to Rhea");
  });
</script>

<svelte:head>
  <title>Rhea • Curriculum Designer</title>
  <link rel="icon" href="favicons/favicon.ico" />
</svelte:head>

<div id="hub-container" class="container">
  <header id="hub-header">
    <div class="header-controls">
      <ThemeSelector />
    </div>
    <div class="header-title">
      <img src="icon.png" alt="Rhea icon" class="header-icon" />
      <h1>Rhea</h1>
    </div>
    <p>Curriculum Design Tools for Peer-Led Learning</p>
  </header>

  <!-- TODO: possibly create hub component? In case I need sub-hubs when functionality expands -->
  <main class="rhea-hub">
    <!-- TODO: address a11y issues from workflow cards -->
    <div class="workflow-cards">
      <WorkflowCard
        workflow="theia"
        icon="/theia/icon.png"
        title="Theia"
        description="Upload and manage previously generated course structures and modules."
        features={["Upload course JSON", "Resume workflows", "Export formats"]}
        actionText="Manage Content →"
        href="/theia/resume"
      />

      <WorkflowCard
        workflow="metis"
        icon="/metis/icon.png"
        title="Metis"
        description="Create a standalone module specification with projects, skills, and research topics."
        features={["Project briefs", "Learning objectives", "Research topics"]}
        actionText="Generate Module →"
        href="/metis/update"
      />

      <WorkflowCard
        workflow="tethys"
        icon="/tethys/icon.png"
        title="Tethys"
        description="Create a linked set of modules."
        features={["Coming Soon"]}
        actionText="Design Arc →"
        href="/tethys/wip"
      />

      <WorkflowCard
        workflow="themis"
        icon="/themis/icon.png"
        title="Themis"
        description="Create a complete multi-week course with interconnected modules and learning progressions."
        features={["Multiple modules", "Course structure", "Learning progression"]}
        actionText="Build Course →"
        href="/themis/generate"
      />
    </div>

    {#if $savedCourses.length > 0}
      <section class="recent-courses">
        <h2>Recent Courses</h2>
        <!-- TODO: create CourseList component -->
        <div class="course-list">
          {#each $savedCourses.slice(0, 3) as course}
            <a href="/course/{course.id}" class="course-item">
              <div class="course-info">
                <h3>{course.title}</h3>
                <ul>
                  <li>{course.logistics.totalWeeks} weeks</li>
                  <li>{course.arcs.length} arcs</li>
                  <li>{countModules(course.arcs)} modules</li>
                </ul>
              </div>
              <div class="course-meta">
                {new Date(course.updatedAt).toLocaleDateString()}
              </div>
            </a>
          {/each}
        </div>
      </section>
    {/if}
  </main>
</div>

<style>
  .container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 2rem;
  }

  header {
    text-align: center;
    margin-bottom: 4rem;
    position: relative;
  }

  .header-controls {
    position: absolute;
    top: 0;
    right: 0;
  }

  .header-title {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 1rem;
    margin-bottom: 0.5rem;
  }

  .header-icon {
    width: 64px;
    height: 64px;
    object-fit: contain;
  }

  header h1 {
    font-size: 3.5rem;
    color: var(--palette-foreground);
    margin: 0;
  }

  header p {
    color: var(--palette-foreground);
    font-size: 1.2rem;
  }

  .rhea-hub {
    max-width: 1400px;
    margin: 0 auto;
  }

  .workflow-cards {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 2rem;
    margin-bottom: 4rem;
  }

  .recent-courses {
    margin-top: 4rem;
    padding-top: 2rem;
    border-top: 1px solid var(--palette-line);
  }

  .recent-courses h3 {
    color: var(--palette-foreground);
    margin-bottom: 1.5rem;
    font-size: 1.5rem;
  }

  .course-list {
    display: flex;
    flex-direction: column;
    gap: 1rem;
  }

  .course-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 1.25rem;
    background: var(--palette-bg-subtle);
    border: 1px solid var(--palette-line);
    border-radius: 8px;
    text-decoration: none;
    color: inherit;
    transition: all 0.2s;
  }

  .course-item:hover {
    border-color: var(--palette-foreground);
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
  }

  /*.course-info p {
    margin: 0;
    color: #666;
    font-size: 0.9rem;
  }*/

  .course-meta {
    color: var(--palette-foreground);
    opacity: 0.6;
    font-size: 0.85rem;
  }

  @media (max-width: 1200px) {
    .workflow-cards {
      grid-template-columns: repeat(2, 1fr);
    }
  }

  @media (max-width: 768px) {
    .workflow-cards {
      grid-template-columns: 1fr;
    }

    header h1 {
      font-size: 2.5rem;
    }

    .container {
      padding: 1rem;
    }
  }
</style>
