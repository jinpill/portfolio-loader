<footer>
    <p>
        <span>
            이진필
        </span>
        <span>
            <a href="mailto:jinpill.93@gmail.com" target="_blank">
                E-mail
            </a>
        </span>
        <span>
            <a href="https://github.com/jinpill" target="_blank">
                GitHub
            </a>
        </span>
    </p>
    <p>
        <span>
            Copyright 2022 All rights reserved by Jinpill Lee
        </span>
    </p>
</footer>



<style lang="scss">
    footer {
        flex: none;
        display: flex;
        flex-direction: column;
        align-items: center;
        gap: 8px;
        padding: 20px 20px 32px;

        border-top: 1px solid #d3d3d3;
        background-color: #e8e8e8;
    }

    p {
        flex: none;
        margin: 0;

        color: #808080;
        font-size: 0;

        span {
            font-size: 16px;

            &:not(:first-child)::before {
                margin: 0 8px;
                content: "|";

                color: #acacac;
                font-size: 0.8em;
                user-select: none;
            }
        }
    }

    a {
        color: inherit;

        &:not(:hover) {
            text-decoration: none;
        }
    }
</style>