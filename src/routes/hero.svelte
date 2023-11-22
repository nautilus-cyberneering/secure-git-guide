<div class="hero-section">
    <div class="hero-img">
        <img src="./docs/media/HEADER/SGG-IndexHeader-1600x500.jpg" alt="secure git guide logo">
    </div>
    <div class="hero-flex">
        <div class="hero-secure-git-guide">
            <p class="hero-text">
            Here we attempt to help any developer to use GIT and GitHub more securely. Any challenges that we are facing in terms of security will be registered here. You will find information on how to use GIT securely in development work. This guide is the result of our team actively developing a pure GIT/GitHub infrastructure for repository optimization and automation during which we encountered security challenges.
            </p>
        </div>
        <div class='hero-topics'>
            <div class="hero-topics-heading">
                <h3>Topics that are covered among others:</h3>
            </div>
            <ul class="hero-topics-list">
            <li>GPG and how we use it for projects.</li>
            <li>GPG basic and more advanced uses in relation to GIT and GitHub.</li>
            <li>GitHub secrets, tokens, etc.</li>
            <li>GIT vs GitHub differences.</li>
            <li>etc.</li>
            </ul>
        </div>
    </div>
</div>

<style>
    .hero-section{
        margin-top: -4rem;
    }

    .hero-img img {
        border-radius: 0px;
    }

    .hero-secure-git-guide{
        background-color: lightblue;
        text-align: justify;
    }

    .hero-text{
        padding: 2rem;
        font-weight: 600;
        font-size: 1.1rem;
    }

    .hero-topics{
        display: flex;
        flex-direction: column;
        background-color: navajowhite;
    }

    .hero-topics-heading{
        text-align: center;
        padding: 1rem;
        background-color: black;
    }

    .hero-topics-heading h3{
        color: #fff;
    }

    .hero-topics-list{
        padding: 2rem;
        font-weight: 600;
        font-size: 1.1rem;
        background-color: navajowhite;
        font-size: 1.3rem;
        list-style: inside;
    }

    @media (min-width: 768px){
       .hero-flex{
        display: flex;
       }

       .hero-secure-git-guide{
        flex: 1 1 0px;
       }

       .hero-text{
        font-size: 1.4rem;
        padding-block: 1.5em;
       }

       .hero-topics{
        flex: 1 1 0px;
       }

       .hero-topics-list{
        font-size: 1.4rem;
        flex: 1 1 0px;
       }
    }
</style>