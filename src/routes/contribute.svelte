<div class="contribute-section">
    <div class="contribute-grid">
        <div class="contribute-main">
          <h3 class="contribute-main-heading">We Are All Ears!</h3>
          <p class="contribute-main-text">If you would like to contribute with your experience, have a question or would like to make a correction or suggestion, you are more than welcome. We encourage you to tell us. This is a team effort meant to benefit everyone.</p>
          <p class="contribute-main-text">If you would like to do so, please go and open a discussion on the topic, challenge you are facing, improvement we could make etc. </p>
        </div>
        <div class="contribute-list">
          <h3 class="contribute-list-heading">How we work:</h3>
          <ul class="contribute-list-list">
            <li>A new discussion is opened</li>
            <li>We discuss it publicly with you</li>
            <li>If it makes a new addition to the guide, we create an issue</li>
            <li>You can create the new document or extend or amend an existing one</li>
            <li>You can go <a href='https://github.com/Nautilus-Cyberneering/secure-git-guide/discussions'>here</a> to set up a new discussion. We look forward to seeing your work!.</li>
          </ul>
        </div>
        <div class="contribute-image">
          <img src="https://images.unsplash.com/photo-1531537571171-a707bf2683da?ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D&auto=format&fit=crop&w=774&q=80" alt="person listening to another person">
        </div>
    </div>
    <div class="contribute-link-div">
      <a href="#contact" class="contribute-link">Get in contact with us!</a>
    </div>
</div>

<style>
    .contribute-section{
        margin-top: var(--size-7);
    }

    .contribute-main{
        background-color: lightpink;
        text-align: center;
    }

    .contribute-main p:nth-child(3) {
        padding-bottom: 2rem;
    }

    .contribute-main-heading{
        margin: 0 auto;
        font-size: 1.7rem;
        padding-top: 2rem;
    }

    .contribute-main-text{
        padding-inline: 2rem;
        padding-top: 1rem;
        margin: 0 auto;
        text-align: justify;
    }

    .contribute-list{
        background-color: linen;
        padding: 1.5rem 2rem;
    }

    .contribute-list-list{
        list-style: inside;
        margin-top: 1.5rem;
    }

    .contribute-list-heading{
        margin: 0 auto;
        text-align: center;
        font-size: 1.7rem;
    }

    .contribute-image{
        display: none;
    }

    .contribute-link-div{
        background-color: lightblue;
        padding: 1rem;
        text-decoration: none;
        text-align: center;
    }

    .contribute-link{
        text-decoration: none;
        color: #000;
        font-size: 1.1rem;
    }

    .contribute-link:hover{
        text-decoration: underline;
        color: var(--surface-1);
    }

    @media (min-width: 769px){
        .contribute-main-text{
            font-size: 1.3rem;
        }

        .contribute-list-list{
            font-size: 1.3rem;
        } 
    }

    @media (min-width: 1000px){
        .contribute-grid{
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            grid-template-rows: repeat(2, 1fr);
        }

        .contribute-main{
            grid-area: 1 / 2 / 3 / 3;
        }

        .contribute-list-heading{
            padding-top: .4rem;
        }

        .contribute-list{
            grid-area: 1 / 1 / 3 / 2;
        }

        .contribute-image{
            display: block;
            grid-area: 1 / 3 / 3 / 4;
        }

        .contribute-image img{
            border-radius: 0px;
            object-fit: cover;
            width: 100%;
            height: 100%;
        }

        .contribute-link{
            font-size: 1.4rem;
        }
    }
</style>