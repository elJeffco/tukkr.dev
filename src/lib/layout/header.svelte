<script lang="ts">
    import { page } from '$app/state';

    // verify the current url pathname equals the menu href
    let isCurrentPage = (path: string): boolean => page.url.pathname.split('/')[1] === path;

    // check the scroll state to decrease the header padding
    let scrollY: number = $state(0);
    let scrollOffsetClass = (): string | null => (scrollY > 1 ? 'scrolled' : null);

    // toggle mobile menu state
    let mobileMenuActive: boolean = $state(false);
    let toggleMobileMenu = (): boolean => (mobileMenuActive = !mobileMenuActive);

    // inner window width size state
    let innerWidth: number = $state(0);

    $effect(() => {
        // remove mobileMenuActive true state on mobile breakpoint
        if (innerWidth > 744) mobileMenuActive = false;

        // hide mobile menu on page url change
        if (page.url) {
            mobileMenuActive = false;
        }
    });
</script>

<svelte:window bind:scrollY bind:innerWidth />

<header class={scrollOffsetClass()}>
    <div class="logo">
        <a href="/">
            <img alt="tukkr logo" height="42" width="42" src="/logo.svg" />
        </a>
    </div>
    <button aria-controls="main-menu" aria-expanded={mobileMenuActive ? 'true' : 'false'} aria-label="Toggle main menu" onclick={toggleMobileMenu}>
        <svg class:active={mobileMenuActive} viewBox="0 0 100 100" width="42" height="42">
            <path d="m 70,32 h -40 c 0,0 -8.5,-0.149796 -8.5,8.5 0,8.649796 8.5,8.5 8.5,8.5 h 20 v -20" />
            <path d="m 70,49 h -40" />
            <path d="m 30,66 h 40 c 0,0 8.5,0.15 8.5,-8.5 0,-8.65 -8.5,-8.5 -8.5,-8.5 h -20 v 20" />
        </svg>
    </button>
    <nav aria-label="Main menu" class:active={mobileMenuActive} id="main-menu">
        <a class:active={isCurrentPage('')} href="/">Home</a>
        <a class:active={isCurrentPage('about')} href="/about">About</a>
        <a class:active={isCurrentPage('snippets')} href="/snippets">Snippets</a>
        <a class:active={isCurrentPage('contact')} href="/contact">Contact</a>
    </nav>
</header>

<style lang="scss">
    header {
        position: fixed;
        width: 100%;
        display: flex;
        flex-direction: row;
        flex-wrap: wrap;
        justify-content: space-between;
        align-items: center;
        will-change: padding;
        padding: 1.8rem 2rem;
        background-color: rgba(255, 255, 255, 0.94);
        transition: padding 0.2s ease-in-out;
        z-index: 999;

        // change padding if window is scrolled
        &.scrolled {
            padding: 1.2rem 2rem;
        }

        // hamburger menu
        button {
            padding: 0;
            background: none;
            border: none;
            cursor: pointer;

            @include mediaQuery(s) {
                display: none;
            }

            // svg layout and animation
            svg {
                user-select: none;
                will-change: transform, stroke-dashoffset;
                transition:
                    transform $animation-duration-slow ease-in-out,
                    stroke-dashoffset $animation-duration-slow ease-in-out;

                path {
                    fill: none;
                    stroke: $color-font-dark;
                    stroke-width: 6;
                    stroke-linecap: round;
                }

                &:first-child {
                    stroke-dasharray: 40 121;
                }

                &:last-child {
                    stroke-dasharray: 40 121;
                }

                &.active {
                    transform: rotate(45deg);

                    &:first-child {
                        stroke-dashoffset: -68px;
                    }

                    &:last-child {
                        stroke-dashoffset: -68px;
                    }
                }
            }
        }

        // navigation
        nav {
            display: flex;
            flex-direction: column;
            gap: 1rem;
            width: 100%;
            height: 0;
            overflow: hidden;
            margin-top: 0;
            transition:
                height $animation-duration-slow ease-in-out,
                margin-top $animation-duration-slow ease-in-out;

            &.active {
                // note: height must be a fixed value for transition to work properly
                // when adding or removing a menu item, recalculate the height
                height: 200px;
                margin-top: 2rem;
            }

            @include mediaQuery(s) {
                flex-direction: row;
                width: auto;
                height: auto !important;
                margin-top: 0 !important;

                // disable transition to prevent weird rescaling animations
                transition: none;
            }

            a {
                padding: 0.6rem 0.4rem;

                &.active {
                    font-weight: $font-weight-primary-medium;
                    color: $color-font-dark;
                }
            }
        }
    }
</style>
